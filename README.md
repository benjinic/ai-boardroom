<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>AI 董事會控制台</title>
<style>
body{font-family:-apple-system,sans-serif;background:#f5f7fb;color:#182334;margin:0;padding:16px}
main{max-width:760px;margin:auto}
section{background:white;border-radius:14px;padding:18px;margin:14px 0;box-shadow:0 2px 10px #0001}
h1{font-size:25px}
h2{font-size:18px}
textarea,select{box-sizing:border-box;width:100%;padding:12px;margin:8px 0;border:1px solid #ccd3dd;border-radius:9px;font-size:16px}
button{background:#2459d3;color:white;border:0;border-radius:9px;padding:12px 16px;margin:5px 3px 5px 0;font-size:15px}
pre{white-space:pre-wrap;overflow-wrap:anywhere;background:#edf2fa;padding:12px;border-radius:9px}
small{color:#667085}
</style>
</head>
<body>
<main>
<h1>🏛️ AI 董事會控制台</h1>
<p>使用現有 AI 訂閱，手動同步各 AI 發言，無需 API。</p>
<section>
<h2>1. 會議議題</h2>
<textarea id="topic" rows="3" placeholder="輸入會議議題"></textarea>
<h2>2. 指定發言成員</h2>
<select id="member">
<option value="ChatGPT｜CEO">ChatGPT｜CEO</option>
<option value="Claude｜策略總監">Claude｜策略總監</option>
<option value="Gemini｜市場研究員">Gemini｜市場研究員</option>
<option value="DeepSeek｜財務及技術顧問">DeepSeek｜財務及技術顧問</option>
</select>
<h2>3. 會議階段</h2>
<select id="stage">
<option value="獨立分析">第一輪：獨立分析</option>
<option value="交叉質疑">第二輪：交叉質疑</option>
<option value="最終決策">第三輪：最終決策</option>
</select>
<button onclick="makePrompt()">產生發言指令</button>
<pre id="prompt">指令會顯示在這裏</pre>
<button onclick="copyPrompt()">複製指令</button>
</section>
<section>
<h2>4. 記錄 AI 發言</h2>
<textarea id="reply" rows="5" placeholder="將 AI 回覆貼在這裏"></textarea>
<button onclick="saveReply()">儲存發言</button>
<button onclick="exportNotes()">匯出紀錄</button>
<div id="history"></div>
</section>
<small>會議紀錄保存在此瀏覽器的本地儲存空間。請定期匯出備份。</small>
</main>
<script>
let records=[];
try{records=JSON.parse(localStorage.getItem('boardRecords')||'[]')}catch(e){}
function el(id){return document.getElementById(id)}
function makePrompt(){
const previous=records.map(r=>r.member+'（'+r.stage+'）：'+r.reply).join('\n\n');
el('prompt').textContent='你是AI董事會成員：'+el('member').value+'\n會議議題：'+el('topic').value+'\n階段：'+el('stage').value+'\n請提出獨立判斷，並對其他成員的意見作批判分析。不要假裝直接連接其他AI。\n\n已有會議發言：\n'+(previous||'暫無');
}
async function copyPrompt(){
const t=el('prompt').textContent;
try{await navigator.clipboard.writeText(t);alert('已複製')}catch(e){alert('請長按指令文字並選擇複製')}
}
function saveReply(){
const reply=el('reply').value.trim();
if(!reply){alert('請先貼上AI回覆');return}
records.push({member:el('member').value,stage:el('stage').value,reply});
try{localStorage.setItem('boardRecords',JSON.stringify(records))}catch(e){alert('無法儲存，請先匯出備份')}
el('reply').value='';render();
}
function render(){
el('history').replaceChildren();
records.forEach((r,i)=>{
const d=document.createElement('section');
const h=document.createElement('strong');h.textContent=(i+1)+'. '+r.member+'｜'+r.stage;
const p=document.createElement('pre');p.textContent=r.reply;
d.append(h,p);el('history').append(d);
});
}
function exportNotes(){
const text='AI 董事會會議紀錄\n議題：'+el('topic').value+'\n\n'+records.map(r=>r.member+'｜'+r.stage+'\n'+r.reply).join('\n\n');
const a=document.createElement('a');a.href=URL.createObjectURL(new Blob([text],{type:'text/plain;charset=utf-8'}));a.download='AI董事會會議紀錄.txt';a.click();setTimeout(()=>URL.revokeObjectURL(a.href),1000);
}
render();
</script>
</body>
</html>
