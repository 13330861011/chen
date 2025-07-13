# index.html
<!doctype html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8"/>
    <title>ADHD 番茄钟 Demo</title>
    <meta name="viewport" content="width=device-width,initial-scale=1"/>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet"/>
    <style>
        body{background:#f7f7f7}.timer{font-size:3rem;text-align:center;margin:2rem 0}
    </style>
</head>
<body>
<nav class="navbar navbar-dark bg-dark">
    <div class="container-fluid"><span class="navbar-brand mb-0 h1">ADHD 专注 Demo</span></div>
</nav>

<div class="container mt-4">
    <div class="card mb-4">
        <div class="card-header">今日任务</div>
        <div class="card-body">
            <input id="title" class="form-control mb-2" placeholder="任务标题"/>
            <input id="duration" type="number" class="form-control mb-2" min="1" max="120" value="25"/>
            <button id="addBtn" class="btn btn-primary w-100">添加</button>
        </div>
        <ul id="taskList" class="list-group list-group-flush"></ul>
    </div>

    <div class="card">
        <div class="card-header">番茄钟</div>
        <div class="card-body text-center">
            <div class="timer" id="timer">25:00</div>
            <button id="startBtn" class="btn btn-success">开始</button>
            <button id="resetBtn" class="btn btn-outline-secondary">重置</button>
        </div>
    </div>

    <div class="alert alert-info mt-4" id="encourage">
        今日鼓励加载中...
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/dayjs@1/dayjs.min.js"></script>
<script>
(() => {
    /* 1. URL 参数自动设置番茄时长（手表唤醒） */
    const params = new URLSearchParams(location.search);
    if (params.has('d')) {
        document.getElementById('duration').value = params.get('d');
    }

    /* 2. 任务列表（仅内存） */
    const list = document.getElementById('taskList');
    document.getElementById('addBtn').onclick = () => {
        const title = document.getElementById('title').value.trim();
        const dur   = document.getElementById('duration').value;
        if (!title) return alert('请输入任务');
        const li = document.createElement('li');
        li.className = 'list-group-item d-flex justify-content-between';
        li.innerHTML = `<span>${title} (${dur}min)</span>
                        <button class="btn btn-sm btn-outline-primary use"
                                data-d="${dur}">使用</button>`;
        list.appendChild(li);
        document.getElementById('title').value = '';
    };

    /* 3. 番茄钟逻辑 */
    let sec = 25 * 60, timer = null;
    const timerEl = document.getElementById('timer');
    const startBtn = document.getElementById('startBtn');
    const resetBtn = document.getElementById('resetBtn');

    function render() {
        const m = String(Math.floor(sec / 60)).padStart(2, '0');
        const s = String(sec % 60).padStart(2, '0');
        timerEl.textContent = `${m}:${s}`;
    }
    render();

    list.addEventListener('click', e => {
        if (e.target.classList.contains('use')) {
            sec = +e.target.dataset.d * 60;
            render();
        }
    });

    startBtn.onclick = () => {
        if (timer) return;
        timer = setInterval(() => {
            sec--;
            render();
            if (sec <= 0) {
                clearInterval(timer); timer = null;
                alert('专注完成！'); startBtn.textContent = '开始';
            }
        }, 1000);
        startBtn.textContent = '计时中';
    };
    resetBtn.onclick = () => {
        clearInterval(timer); timer = null;
        sec = +document.getElementById('duration').value * 60;
        render();
        startBtn.textContent = '开始';
    };

    /* 4. 每日一句鼓励语 */
    const quotes = [
        "你已经比昨天的自己更专注了！",
        "完成一个番茄，就离目标更近一步。",
    ];
    document.getElementById('encourage').textContent =
        quotes[Math.floor(Math.random() * quotes.length)];
})();
</script>
</body>
</html>
