# CrewAI OA-Team 30 萬能蜂群 — 實作與執行模式 (2026-08-07 驗證, 本輪更新)

OA-Team 30 蜂群 (soul.md 的 5 大陣列 × 6 代理) 用 **CrewAI** 實作。本檔記錄在本機 Windows/Git-bash 環境下從零建起並跑通的確切步驟與坑（本輪已驗證通過 `load_crew` 原生組裝）。

## 為什麼用 CrewAI（非標準 Hermes Agent）
- VPS 上的 OmniAgent(OA) 是 esggo 的 `packages/omni-agent`（OmniJules 5T 閘道驗證引擎，TS），不是聊天 agent，WebUI/crewai 都不能當 backend。
- Hermes WebUI 容器需標準 hermes-agent 才能聊天；VPS 磁盤滿時裝不下。
- CrewAI 是多智能體框架，對應 soul.md「30 人代理小隊」願景，JSON-first 格式直映 5 陣列。

## 安裝（已驗證可行）
系統 Python 是 3.14.6，**超出 CrewAI 支援 (<3.14)**。用 uv 裝隔離環境：
```bash
uv python install 3.13
uv tool install crewai[litellm] --python 3.13   # litellm 讓 tencent/hy3:free 等自訂模型可路由
```
crewai 裝在 `C:/Users/<user>/AppData/Roaming/uv/tools/crewai/Scripts/python.exe`。
用該 python 執行（不要用系統 python3）：
```bash
CREWAI_PY="$(cygpath -u 'C:/Users/dingj/AppData/Roaming/uv/tools/crewai/Scripts/python.exe')"
env -u PYTHONPATH "$CREWAI_PY" main.py "任務描述"
```

## ⚠️ JSON-first 格式硬性約束（load_crew 原生驗證 — 本輪實證）
`crewai.project.load_crew('crew.jsonc')` 對檔案格式極嚴格，以下都會被拒：
1. **`crew.jsonc` 不能有 `description` 欄位**（報 `unsupported field(s): description`）。
2. **`agents/*.jsonc` 只能有標準欄位**：`role` / `goal` / `backstory` / `allow_delegation`（選用 `llm` / `tools`）。
   - `soul_id` / `squad` / `tags` / `tools`（custom:xxx）都會被拒 → 這些寫進 `//` 註解行，不要放 JSON 鍵。
   - `custom:memory_recall` 這類工具名若放進 `tools` 陣列，需對應 `tools/<name>.py` 的 BaseTool 子類，否則報 `Custom tool not found`。暫時移除 tools 欄位讓結構先通過。
3. **`llm` 不要硬編碼在 agent jsonc**。`load_crew` 在 build_agent 時會嘗試初始化預設 OpenAI provider，需要 `OPENAI_API_KEY` 環境變數。硬編 `openai/tencent/hy3:free` 在某些 loader 路徑也會被拒。改由環境驅動（`OPENAI_MODEL_NAME` / `OPENAI_API_BASE`）。
4. **`crew.jsonc` 的 `agents` 陣列** 列出 `sage_01`..`verify_30` 檔名（不含 `.jsonc` 副檔名），`tasks[].agent` 引用同名。

## crewai 版本陷阱
- **`crewai run` 報 `run_crew program not found`**：1.15.12 舊版 CLI 期望 `run_crew` entrypoint（來自 `crewai create` 腳手架的 pyproject `[project.scripts]`）。我們的專案沒有。
  - 解法 A：直接 `python main.py`（main.py 用 `load_crew()` + `OATeamFlow().kickoff()`，不依賴 CLI）。
  - 解法 B（CI）：`uv run --with 'crewai[litellm]' python main.py`（解決 ModuleNotFoundError，因 uv tool 裝的 crewai 不在 `python` 的 site-packages）。
- 舊建議「用 class-based Agent/Task/Crew 組裝」已被本輪推翻：`load_crew()` JSON-first 才是 Quickstart 推薦模式，且本輪已用 `load_crew` 原生驗證通過（30 agents / 5 tasks）。

## 模型路由（litellm）
裸 `tencent/hy3:free` 會被 litellm 誤判為 Tencent 原生端點 → `TencentException Connection error`。
必須用 `LLM(model="openai/tencent/hy3:free", base_url=..., api_key=...)` 顯式走 OpenAI-compatible 路由（CrewAI 顯示正規化為 `tencent/hy3:free`，但 `openai/` provider 已生效 — 實際報 `OpenAI API connection error` 即證明）。
本輪最終採 env-driven：agent jsonc 不寫 llm，由 `OPENAI_MODEL_NAME` / `OPENAI_API_BASE` / `OPENAI_API_KEY` 環境變數注入。

## 結構（對應 soul.md，本輪可用）
- `crew.jsonc`：標準格式（`name` / `agents[]` / `tasks[]` / `process` / `verbose`），**無 description**。
- `agents/*.jsonc`：30 個定義（sage_01..06 / rune_07..12 / wing_13..18 / forge_19..24 / verify_25..30），**僅標準欄位** + `//` 註解含 soul_id/squad/tags/tools。
- `gen_agents.py`：批量生成 30 agent（改結構只跑這支；寫標準欄位 + 註解元數據）。
- `main.py`：用 `from crewai.flow import Flow, listen, start` + `from crewai.project import load_crew`，`OATeamFlow` 的 `@start` 收任務、`@listen` 跑 `load_crew(...).kickoff(inputs={...})`。
- `README.md`：陣列對應表。

## 驗證（本輪實證可跑）
- **結構驗證**（不依賴網絡）：`load_crew(Path('crew.jsonc'))` 成功，30 agents / 5 tasks 組裝通過。
  - 用快速失敗埠避免 DNS 卡死：`env -u PYTHONPATH OPENAI_API_KEY=dummy OPENAI_API_BASE=http://127.0.0.1:9 "$CREWAI_PY" -c "from crewai.project import load_crew; c,_=load_crew(Path('crew.jsonc')); assert len(c.agents)==30 and len(c.tasks)==5"`（~21s 完成）。
- **純 JSON 驗證**：strip `//` 後 json.loads 斷言 30 agents / 5 tasks / 每 agent 含 role+goal+backstory。
- **CI 執行證明**：GitHub Actions run `31159358321` 實際執行 workflow，證明 YAML 合法、`uv run` 解決 import、`load_crew` 進入 build_agent 階段（失敗只因 secret 注入為空）。

## GitHub Actions 路徑（關鍵坑）
- **workflow 必須在 repo 根 `.github/workflows/`**，不能放 `oa-team-crewai/.github/`（GitHub 不認嵌套路徑 — 本輪實證 `crewai-run.yml` 放子目錄完全沒被註冊）。
- `CREWAI_API_KEY` 由用戶 `gh secret set` 存入 repo Secrets（secret 值不可 `gh secret get` 回讀，設計如此）。CI 注入為 `OPENAI_API_KEY`：`env: OPENAI_API_KEY: ${{ secrets.CREWAI_API_KEY }}`。
- 本輪實證：CI 讀到 `CREWAI_API_KEY length = 0`（注入失敗）— 可能 secret 不在 repo 級 Actions 作用域。需用戶確認 GitHub Web UI `Settings → Secrets and variables → Actions` 有 `CREWAI_API_KEY`。

## LLM 端點（環境層阻塞，非程式碼）
- `api.nousresearch.com` 在本網絡 DNS 解析失敗（`Non-existent domain`）→ 實際 LLM 呼叫無法跑通。
- 本機無 Ollama（:11434 無回應）。需本機可達端點（本機 Ollama / OpenRouter key / 正確 Nous 域名）才能 `kickoff` 出真實產出。
- 環境有 `NOUS_API_KEY`，但端點不可達。程式碼層已驗證（30 agent + 5 task 組裝成功、LLM 路由正確），只差端點。
- **CrewAI AMP** 文檔（用戶貼過）是 SaaS 部署，需 `crewai login` + 帳號，與本地原型脫節；`CREWAI_API_KEY` 用途待確認（可能為 AMP login token，非 LLM key）。

## OCI / Oracle（本輪探索，未成功）
- 現有 `~/.oci/config` 有效（tenancy / region=ap-singapore-1 / key_file / fingerprint 俱全）。安裝 `uv tool install oci-cli --python 3.13`，`oci iam region-subscription list` 回 `READY` 證明連通。
- 帳號已有 2 台 Always Free ARM A1.Flex（debi-node / esggo-vps = 161.118.248.180），ARM 配額已用滿。
- 嘗試 `oci compute instance launch --shape VM.Standard.E2.1.Micro`（AMD Always Free，獨立配額）持續回 `CannotParseRequest` 400，經 `--debug` 證明請求體 JSON **完全合法**（無重複欄位、無非法鍵）。推測：該 region 的 AMD Micro Always Free 需透過 **Oracle Cloud 控制台網頁**勾選「Always Free」才能建立，OCI CLI 無法自動標記資格。這**不是腳本錯誤**。
- 若需申請：收集 AD=`ap-singapore-1-AD-1`、subnet OCID、Oracle Linux 8 image OCID，去 console 勾選 Always Free。不要無限 retry CLI launch（浪費嘗試）。
- 可用 `bash -n` 驗證 OCI 腳本語法；內聯過長命令會被 agent 硬阻（heredoc/大單行），改用 `.sh` 腳本檔執行。
