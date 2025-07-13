import os
import zipfile

# 创建目录
web_dir = "/mnt/data/escape-forest-web"
os.makedirs(web_dir, exist_ok=True)

# index.html
index_html = """
<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>逃出神秘森林</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <div class="container">
    <h1>逃出神秘森林</h1>
    <div id="story" class="story-text">点击开始游戏！</div>
    <div id="event" class="event-text"></div>
    <div id="choices" class="choices"></div>
    <div class="controls">
      <button onclick="startGame()">🎮 开始游戏</button>
      <button onclick="showInventory()">🎒 查看背包</button>
    </div>
    <div id="inventory" class="inventory"></div>
  </div>
  <script src="game.js"></script>
</body>
</html>
"""

# style.css
style_css = """
body {
  font-family: "Helvetica Neue", sans-serif;
  background: #f4f4f4;
  color: #222;
  margin: 0;
  padding: 0;
}
.container {
  max-width: 600px;
  margin: auto;
  padding: 20px;
}
h1 {
  text-align: center;
}
.story-text {
  font-size: 20px;
  margin: 20px 0;
}
.choices button, .controls button {
  display: block;
  width: 100%;
  margin: 10px 0;
  padding: 12px;
  font-size: 16px;
}
.event-text {
  color: #d2691e;
  font-weight: bold;
}
.inventory {
  background: #eee;
  padding: 10px;
  margin-top: 10px;
  display: none;
}
"""

# game.js
game_js = """
let step = 0;
let inventory = [];

function randomEvent() {
  const events = [
    "你听见远处传来狼嚎……",
    "一阵冷风吹来，树叶沙沙作响。",
    "你看到天上乌云密布。",
    "一只猫头鹰盯着你看。"
  ];
  return Math.random() < 0.3 ? events[Math.floor(Math.random() * events.length)] : "";
}

function show(text, choices = []) {
  document.getElementById("story").innerText = text;
  const choicesDiv = document.getElementById("choices");
  choicesDiv.innerHTML = "";
  choices.forEach(({ label, nextStep, action }) => {
    const btn = document.createElement("button");
    btn.innerText = label;
    btn.onclick = () => {
      if (action) action();
      step = nextStep;
      update();
    };
    choicesDiv.appendChild(btn);
  });
  const event = randomEvent();
  document.getElementById("event").innerText = event;
}

function update() {
  if (step === 1) {
    show("你在迷雾森林中醒来，前方有两条路：", [
      { label: "向左走", nextStep: 2 },
      { label: "向右走", nextStep: 3 }
    ]);
  } else if (step === 2) {
    inventory.push("古老钥匙");
    show("你遇到一个神秘老人，他给了你一把古老钥匙。", [
      { label: "继续前进", nextStep: 4 },
      { label: "返回原地", nextStep: 5 }
    ]);
  } else if (step === 3) {
    show("你来到一条小河边，有一艘小船和一座木桥：", [
      { label: "乘船过去", nextStep: 6 },
      { label: "走桥过去", nextStep: 7 }
    ]);
  } else if (step === 4) {
    show("你拿着钥匙打开一道门，成功逃出森林！🎉");
  } else if (step === 5) {
    show("你迷失了方向，无法回到原路。💀 游戏结束。");
  } else if (step === 6) {
    show("船顺利将你送到对岸，你逃出森林！🎉");
  } else if (step === 7) {
    show("桥突然断裂，你掉进了河里！💀 游戏结束。");
  }
}

function startGame() {
  step = 1;
  inventory = [];
  update();
  document.getElementById("inventory").style.display = "none";
}

function showInventory() {
  const inv = document.getElementById("inventory");
  inv.innerHTML = "<strong>你的背包：</strong><br>" + (inventory.length ? inventory.join("<br>") : "（空）");
  inv.style.display = "block";
}
"""

# 写入文件
with open(f"{web_dir}/index.html", "w", encoding="utf-8") as f:
    f.write(index_html)

with open(f"{web_dir}/style.css", "w", encoding="utf-8") as f:
    f.write(style_css)

with open(f"{web_dir}/game.js", "w", encoding="utf-8") as f:
    f.write(game_js)
