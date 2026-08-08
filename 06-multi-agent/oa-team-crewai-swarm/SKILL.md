---
name: oa-team-crewai-swarm
category: autonomous-ai-agents
description: Build OA-Team 30-agent CrewAI swarm from soul.md JSON-first.
tags: [crewai, swarm, multi-agent, oa-team, json-first, soul-md]
---

# OA-Team 30 蜂群 — CrewAI 實作 (JSON-first)

對應 `soul.md` 的「30 Souls Matrix」(5 大陣列 × 6 代理)。本技能是經實戰驗證的可執行骨架與排錯手冊，補足 `oa-team-swarm` (Hermes 委派版) 之外的 CrewAI 實作路徑。

## 1. 目錄結構

```
oa-team-crewai/
├── crew.jsonc              # CrewAI JSON-first 編排 (agents 陣列 + tasks 陣列)
├── agents/
│   ├── sage_01.jsonc ... sage_06.jsonc       # 智庫聖所
│   ├── rune_07.jsonc ... rune_12.jsonc       # 符文契約
│   ├── wing_13.jsonc ... wing_18.jsonc       # 光之羽翼
│   ├── forge_19.jsonc ... forge_24.jsonc     # 煉金熵減
│   └── verify_25.jsonc ... verify_30.jsonc   # 5T 驗算
├── main.py                 # Flow 入口 (load_crew + OATeamFlow)
├── gen_agents.py           # 批量生成 30 agent (對應 soul.md 5 陣列)
├── pyproject.toml
└── .github/workflows/crewai-run.yml   # 注意: 必須在 repo 根 .github/workflows/
```

## 2. crew.jsonc (標準格式)

```jsonc
{
  "name": "oa_team_30_swarm",
  "agents": [ "sage_01", /* ... 30 個, 對應 agents/<name>.jsonc */ "verify_30" ],
  "tasks": [
    {
      "name": "extract_essence",
      "description": "【本質提純】由智庫聖所(01-06)... 任務：{task}",
      "expected_output": "結構化的任務本質脈絡與相關記憶召回",
      "agent": "sage_01"
    }
    /* 5 tasks: extract → forge_contract → dispatch_swarm → entropy_forge → verify_5t */
  ],
  "process": "sequential",
  "verbose": true
}
```

**禁止欄位**：`crew.jsonc` 不能有 `description`；`agents/*.jsonc` 不能有 `soul_id`/`squad`/`tags` 等非標準鍵 → `load_crew` 報 `unsupported field(s)`。元數據寫進 `//` 註解行。

## 3. agents/<name>.jsonc (標準欄位)

```jsonc
// 智庫聖所小隊 — 智庫聖所 · 記憶召喚師 (Agent 01)
// soul_id: OA-01 | tags: #記憶聖所 #全知之眼
{
  "role": "智庫聖所 · 記憶召喚師 (Agent 01)",
  "goal": "維護長短期記憶召回率 >95%...",
  "backstory": "OA-Team 30 蜂群的記憶中樞...",
  "allow_delegation": true
}
```

**llm 不要硬編碼**：交給環境變數 (`OPENAI_MODEL_NAME` / `OPENAI_API_BASE` / `OPENAI_API_KEY`) 驅動，本地 (Nous) 與 CI (CREWAI_API_KEY) 皆可用。

## 4. main.py (Flow + load_crew)

```python
import os, sys
from pathlib import Path
from crewai.flow import Flow, listen, start
from crewai.project import load_crew
from pydantic import BaseModel

class SwarmState(BaseModel):
    task: str = ""
    result: str = ""

class OATeamFlow(Flow[SwarmState]):
    @start()
    def prepare_task(self, crewai_trigger_payload: dict | None = None):
        self.state.task = sys.argv[1] if len(sys.argv) > 1 else "為 ESG-GO 產出一個 5T 合規的元件骨架"
        print("🐝 OA-Team 30 萬能蜂群啟動")

    @listen(prepare_task)
    def run_swarm(self):
        crew, default_inputs = load_crew(Path(__file__).with_name("crew.jsonc"))
        result = crew.kickoff(inputs={**default_inputs, "task": self.state.task})
        self.state.result = result.raw
        return result

if __name__ == "__main__":
    OATeamFlow().kickoff()
```

## 5. 環境與執行

### 安裝 (Python 3.10–3.13, CrewAI 不支援 3.14)
```bash
uv python install 3.13
uv tool install crewai --python 3.13
uv tool install 'crewai[litellm]' --python 3.13   # LiteLLM 路由自訂模型
```

### 本機執行 (Windows uv tool)
```bash
CREWAI_PY="$HOME/AppData/Roaming/uv/tools/crewai/Scripts/python.exe"
# 關鍵: 清除 PYTHONPATH 污染 (否則 import pydantic 崩潰)
env -u PYTHONPATH OPENAI_API_KEY="$NOUS_API_KEY" \
  OPENAI_API_BASE="https://api.nousresearch.com/v1" \
  "$CREWAI_PY" main.py "任務描述"
```

### CI 執行 (GitHub Actions)
```yaml
# .github/workflows/crewai-run.yml  (必須 repo 根, 子目錄不會被註冊!)
jobs:
  crewai-run:
    runs-on: ubuntu-latest
    defaults: { run: { working-directory: oa-team-crewai } }
    steps:
      - uses: actions/checkout@v4
      - run: curl -LsSf https://astral.sh/uv/install.sh | sh
      - name: Run
        env:
          OPENAI_API_KEY: ${{ secrets.CREWAI_API_KEY }}
          OPENAI_MODEL_NAME: gpt-4o-mini
        run: |
          export PATH="$HOME/.local/bin:$PATH"
          uv run --python 3.13 --with 'crewai[litellm]' python main.py "任務"
```

## 6. 已驗證的坑 (error → fix)

| 錯誤 | 根因 | 修復 |
|------|------|------|
| `No module named 'pydantic_core._pydantic_core'` | `PYTHONPATH` 指向損壞的 Hermes venv，污染 crewai | 執行前 `env -u PYTHONPATH` |
| `crewai: command not found` (但 `uv tool list` 有) | `~/.local/bin` 不在 PATH | `export PATH="$HOME/.local/bin:$PATH"` |
| `Failed to spawn: run_crew / program not found` | `crewai run` CLI 期望 `crewai create` 腳手架的 entrypoint | 改用 `python main.py` 或 `uv run --with crewai python main.py` |
| `crew.jsonc: unsupported field(s): description` | `load_crew` 拒絕非標準鍵 | 刪除 crew 層 description |
| `agent: unsupported field(s): soul_id, squad, tags` | agent JSON 只收標準欄位 | 元數據移到 `//` 註解 |
| `custom:memory_recall not found` | 宣告不存在的 custom tool | 先移除 tools 欄位，或建 `tools/<name>.py` |
| `Error importing native provider: Missing credentials` | `OPENAI_API_KEY` 空 (CI 讀不到 secret) | 確認 secret 在 repo 級 Actions scope；CI 用 `echo "len=${#KEY}"` 探針除錯 |
| `TencentException - Connection error` (模型 `tencent/hy3:free`) | 裸模型名被 litellm 誤判為 Tencent 原生端點 | 用 `openai/tencent/hy3:free` + `base_url` 走 OpenAI-compatible |
| `Failed to connect to OpenAI API` (模型 `openai/...`) | 端點 DNS 失敗/不可達 | 換可達端點 (本機 Ollama / OpenRouter / 正確域名) |
| GitHub Actions 不觸發 workflow | workflow 放進 `oa-team-crewai/.github/` 子目錄 | 移到 repo 根 `.github/workflows/` |

## 7. 驗證清單 (不依賴網絡)

```bash
# 結構驗證 (jsonc 解析 + 數量)
python3 -c "
import json, os
def strip(p): return '\n'.join(l for l in open(p,encoding='utf-8') if not l.strip().startswith('//'))
crew = json.loads(strip('crew.jsonc'))
assert len(crew['agents'])==30 and len(crew['tasks'])==5
af = sorted(f for f in os.listdir('agents') if f.endswith('.jsonc'))
assert len(af)==30
print('✅ 30 agents / 5 tasks 結構正確')
"
# CrewAI 原生組裝驗證 (用快速失敗埠代替空 key, 避免網絡卡死)
env -u PYTHONPATH OPENAI_API_KEY=dummy OPENAI_API_BASE=http://127.0.0.1:9 \
  "$CREWAI_PY" -c "from crewai.project import load_crew; c,_=load_crew(__import__('pathlib').Path('crew.jsonc')); assert len(c.agents)==30"
```

## 8. 與 oa-team-swarm 的關係
`oa-team-swarm` 是 Hermes Agent 委派版 (delegate_task / agents-cli)；本技能是 CrewAI 原生多智能體版。兩者共用 soul.md 的 5 大陣列角色定義與 5T 協議，但執行引擎不同。
