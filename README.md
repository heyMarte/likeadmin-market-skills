# 算力超市skills

为兼容 Agent Skills 的 AI Agent 提供算力超市模型与应用 API 的智能编排能力。技能会先发现当前租户可用的模型和应用，再选择兼容的工作流；本地媒体会在确认后通过上传接口转换为可提交的 URL。

## Install

```bash
npx skills add marte95/likeadmin-market-skills
```

OpenClaw users can install the same skill with:

```bash
npx clawhub@latest install likeadmin-market-skills
```

## Configure

Provide your tenant API origin and API key, then save them to the local credential store. This does not depend on the agent-specific installation directory:

```bash
export LIKEADMIN_MARKET_BASE_URL="https://your-tenant-api-origin"
export LIKEADMIN_MARKET_API_KEY="your-api-key"
node -e 'const fs=require("fs");const path=require("path");const dir=path.join(process.env.HOME,".likeadmin-market-skills");const file=path.join(dir,"credentials.json");fs.mkdirSync(dir,{recursive:true,mode:0o700});fs.chmodSync(dir,0o700);fs.writeFileSync(file,JSON.stringify({base_url:process.env.LIKEADMIN_MARKET_BASE_URL,api_key:process.env.LIKEADMIN_MARKET_API_KEY},null,2)+"\n",{mode:0o600});fs.chmodSync(file,0o600)'
unset LIKEADMIN_MARKET_BASE_URL LIKEADMIN_MARKET_API_KEY
test -f "$HOME/.likeadmin-market-skills/credentials.json" && echo "Credentials saved"
```

Credentials are stored only in `~/.likeadmin-market-skills/credentials.json` with restrictive permissions. The skill never hardcodes a tenant URL or API key.

See [SKILL.md](SKILL.md) for workflow selection, supported orchestration recipes, and media-upload safeguards.
