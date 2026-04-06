---
description: Run a Gemini code review against local git state
argument-hint: '[--wait|--background] [--base <ref>] [--scope auto|working-tree|branch]'
disable-model-invocation: true
allowed-tools: Read, Glob, Grep, Bash(node:*), Bash(git:*), AskUserQuestion
---

Run a Gemini code review.

Raw slash-command arguments:
`$ARGUMENTS`

Core constraint:
- This command is review-only.
- Do not fix issues, apply patches, or suggest that you are about to make changes.
- Your only job is to run the review and return Gemini's output verbatim to the user.

Execution mode rules:
- If `--wait` is in the arguments, run in the foreground without asking.
- If `--background` is in the arguments, run in the background without asking.
- Otherwise, estimate the size using `git status --short` and `git diff --shortstat`:
  - Recommend waiting only for 1-2 file diffs. Recommend background for everything else.
  - Use `AskUserQuestion` exactly once with: `Wait for results` and `Run in background`

Foreground flow:
- Run the review. The node wrapper streams output in real-time, creates `~/.gemini-plugin` if needed, saves output only on success, and propagates the exit code:
```bash
node -e "const {spawn,spawnSync:ss}=require('child_process'),os=require('os'),fs=require('fs'),c=require('crypto'),path=require('path');let t;try{t=ss('git',['rev-parse','--show-toplevel'],{encoding:'utf8'}).stdout.trim()}catch(e){t=''};const h=t?c.createHash('md5').update(t).digest('hex'):'global';const d=os.homedir()+'/.gemini-plugin';try{fs.mkdirSync(d,{recursive:true})}catch(e){};const args=process.argv.slice(2).filter(Boolean);const child=spawn(process.execPath,[path.resolve(process.env.CLAUDE_PLUGIN_ROOT,'scripts/gemini-companion.mjs'),'review',...args],{stdio:['inherit','pipe','inherit'],env:process.env});const chunks=[];child.stdout.on('data',chunk=>{process.stdout.write(chunk);chunks.push(chunk)});child.on('close',code=>{if(code===0)try{fs.writeFileSync(path.join(d,'last-review-'+h+'.md'),Buffer.concat(chunks))}catch(e){};process.exit(code??1)});" -- $ARGUMENTS
```
- Return stdout verbatim. Do not fix any issues mentioned.

Background flow:
```typescript
Bash({
  command: `node -e "const {spawn,spawnSync:ss}=require('child_process'),os=require('os'),fs=require('fs'),c=require('crypto'),path=require('path');let t;try{t=ss('git',['rev-parse','--show-toplevel'],{encoding:'utf8'}).stdout.trim()}catch(e){t=''};const h=t?c.createHash('md5').update(t).digest('hex'):'global';const d=os.homedir()+'/.gemini-plugin';try{fs.mkdirSync(d,{recursive:true})}catch(e){};const args=process.argv.slice(2).filter(Boolean);const child=spawn(process.execPath,[path.resolve(process.env.CLAUDE_PLUGIN_ROOT,'scripts/gemini-companion.mjs'),'review',...args],{stdio:['inherit','pipe','inherit'],env:process.env});const chunks=[];child.stdout.on('data',chunk=>{process.stdout.write(chunk);chunks.push(chunk)});child.on('close',code=>{if(code===0)try{fs.writeFileSync(path.join(d,'last-review-'+h+'.md'),Buffer.concat(chunks))}catch(e){};process.exit(code??1)});" -- $ARGUMENTS`,
  description: "Gemini review",
  run_in_background: true
})
```
- Tell the user: "Gemini review started in the background. Check `/gemini:status` for progress."
