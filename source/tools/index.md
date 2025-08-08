---
title: 在线工具
date: 2025-08-08 21:41:58
---

<div id="tools-container">  
  <!-- 时间戳转时间工具 -->
  <div class="tool-section">
    <h3>时间戳转换</h3>
    <input type="number" id="timestamp" placeholder="请输入时间戳（秒/毫秒）">
    <button onclick="convertTimestamp()">转换</button>
    <button onclick="setCurrentTimestamp()">当前时间戳</button>
    <p id="current-time-info"></p>
    <p id="time-result"></p>
  </div>
  
  <!-- 随机字符串生成工具 -->
  <div class="tool-section">
    <h3>随机字符串生成</h3>
    <div>
      <label>
        <input type="checkbox" id="uppercase" checked> 
        大写字母 (A-Z)
      </label>
    </div>
    <div>
      <label>
        <input type="checkbox" id="lowercase" checked> 
        小写字母 (a-z)
      </label>
    </div>
    <div>
      <label>
        <input type="checkbox" id="numbers" checked> 
        数字 (0-9)
      </label>
    </div>
    <div>
      <label>
        <input type="checkbox" id="symbols"> 
        符号 (!@#$%^&*等)
      </label>
    </div>
    <div>
      <label>长度: <input type="number" id="random-length" value="10" min="1" max="100"></label>
    </div>
    <div>
      <label>排除字符: <input type="text" id="exclude-chars" placeholder="输入要排除的字符"></label>
    </div>
    <button onclick="generateRandomString()">生成</button>
    <button onclick="clearRandomResult()">清空</button>
    <textarea id="random-result" rows="4" cols="50" placeholder="生成的随机字符串将显示在这里"></textarea>
  </div>
  
  <!-- 字符串长度计算工具 -->
  <div class="tool-section">
    <h3>字符串长度计算</h3>
    <textarea id="string-input" placeholder="请输入字符串" rows="4" cols="50"></textarea>
    <button onclick="calculateStringLength()">计算长度</button>
    <p id="length-result"></p>
  </div>
</div>

<script>
// 页面加载时显示当前时间信息
window.onload = function() {
  updateCurrentTimeInfo();
  setInterval(updateCurrentTimeInfo, 1000); // 每秒更新一次当前时间
};

// 更新当前时间信息
function updateCurrentTimeInfo() {
  const now = new Date();
  const currentTimestampSec = Math.floor(now.getTime() / 1000);
  const currentTimestampMs = now.getTime();
  
  const currentTimeInfo = document.getElementById('current-time-info');
  currentTimeInfo.innerHTML = `
    当前时间: ${now.toLocaleString()}<br>
    当前时间戳(秒): ${currentTimestampSec}<br>
    当前时间戳(毫秒): ${currentTimestampMs}
  `;
}

// 设置当前时间戳到输入框
function setCurrentTimestamp() {
  const timestampInput = document.getElementById('timestamp');
  timestampInput.value = Math.floor(new Date().getTime() / 1000);
  convertTimestamp();
}

// 时间戳转时间
function convertTimestamp() {
  let timestamp = document.getElementById('timestamp').value;
  if (!timestamp) {
    alert('请输入时间戳');
    return;
  }
  
  const timeResult = document.getElementById('time-result');
  let date;
  
  // 根据位数判断是秒还是毫秒
  if (timestamp.length >= 13) {
    // 毫秒时间戳
    date = new Date(parseInt(timestamp));
  } else {
    // 秒时间戳
    date = new Date(parseInt(timestamp) * 1000);
  }
  
  // 检查日期是否有效
  if (isNaN(date.getTime())) {
    timeResult.innerHTML = '无效的时间戳';
    return;
  }
  
  // 转换为多种格式
  const formats = [
    `标准时间: ${date.toString()}`,
    `ISO格式: ${date.toISOString()}`,
    `本地格式: ${date.toLocaleString()}`,
    `UTC格式: ${date.toUTCString()}`,
    `时间: ${date.toLocaleTimeString()}`
  ];
  
  timeResult.innerHTML = formats.join('<br>');
}

// 生成随机字符串
function generateRandomString() {
  // 获取用户设置
  const length = parseInt(document.getElementById('random-length').value) || 10;
  const useUppercase = document.getElementById('uppercase').checked;
  const useLowercase = document.getElementById('lowercase').checked;
  const useNumbers = document.getElementById('numbers').checked;
  const useSymbols = document.getElementById('symbols').checked;
  const excludeChars = document.getElementById('exclude-chars').value;
  
  // 构建字符集
  let charset = '';
  if (useUppercase) charset += 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  if (useLowercase) charset += 'abcdefghijklmnopqrstuvwxyz';
  if (useNumbers) charset += '0123456789';
  if (useSymbols) charset += '!@#$%^&*()_+-=[]{}|;:,.<>?';
  
  // 排除指定字符
  if (excludeChars && charset) {
    for (let i = 0; i < excludeChars.length; i++) {
      charset = charset.replace(new RegExp(excludeChars[i], 'g'), '');
    }
  }
  
  // 检查字符集是否为空
  if (!charset) {
    document.getElementById('random-result').value = '错误：请选择至少一种字符类型或减少排除字符';
    return;
  }
  
  // 生成随机字符串
  let randomString = '';
  for (let i = 0; i < length; i++) {
    const randomIndex = Math.floor(Math.random() * charset.length);
    randomString += charset[randomIndex];
  }
  
  document.getElementById('random-result').value = randomString;
}

// 清空随机字符串结果
function clearRandomResult() {
  document.getElementById('random-result').value = '';
}

// 计算字符串长度
function calculateStringLength() {
  const stringInput = document.getElementById('string-input').value;
  const lengthResult = document.getElementById('length-result');
  
  if (stringInput === undefined || stringInput === null) {
    lengthResult.textContent = '请输入字符串';
    return;
  }
  
  lengthResult.textContent = `字符串长度: ${stringInput.length} 字符`;
}
</script>

<style>
.tool-section {
  margin: 20px 0;
  padding: 15px;
  border: 1px solid #eee;
  border-radius: 5px;
}

.tool-section h3 {
  margin-top: 0;
}

.tool-section input, .tool-section textarea {
  margin: 5px;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.tool-section button {
  background-color: #4CAF50;
  border: none;
  color: white;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 4px;
}

.tool-section button:hover {
  background-color: #45a049;
}

.tool-section p {
  margin-top: 10px;
  padding: 10px;
  background-color: #f9f9f9;
  border-left: 4px solid #4CAF50;
}

#current-time-info {
  background-color: #e8f5e9;
  padding: 10px;
  border-radius: 4px;
  font-size: 14px;
}

.tool-section textarea {
  width: 100%;
  margin: 10px 0;
}
</style>