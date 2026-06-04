---
title: 诗句库
date: 2024-01-01 00:00:00
layout: page
comments: false
---

<div class="poems-simple-container">
  <header class="poems-simple-header">
    <h1>📚 诗句库</h1>
    <p class="subtitle">正在加载诗句...</p>
  </header>

  <div class="poems-simple-stats">
    <div class="stat">
      <span class="stat-number">-</span>
      <span class="stat-label">总诗句数</span>
    </div>
  </div>


  <div class="search-simple-box">
    <input type="text" id="search-simple-input" placeholder="🔍 搜索诗句...">
    <button onclick="simpleSearch()">搜索</button>
  </div>

  <div class="poems-simple-list" id="poems-simple-list">
    <div class="loading-simple">正在加载诗句数据...</div>
  </div>

  <div class="export-simple-buttons">
    <button onclick="simpleExportTxt()">📥 导出TXT</button>
    <button onclick="simpleExportJson()">📄 导出JSON</button>
  </div>
</div>

<script>
// 简单版本的诗句加载
function loadSimplePoems() {
  console.log('开始加载诗句...');

  // 直接请求JSON文件
  fetch('/subtitle.json')
    .then(response => {
      console.log('响应状态:', response.status);
      if (!response.ok) {
        throw new Error('网络响应不正常: ' + response.status);
      }
      return response.json();
    })
    .then(poemsData => {
      console.log('成功加载数据:', poemsData);
      
      // 处理数据
      let poems = [];
      if (Array.isArray(poemsData)) {
        poems = poemsData;
      } else if (typeof poemsData === 'object') {
        poems = Object.values(poemsData).flat();
      }
      
      poems = poems.filter(poem => poem && poem.trim().length > 0);
      console.log('处理后的诗句数量:', poems.length);
      
      // 🆕 添加这一行实现倒序
      poems = poems.reverse(); 
      
      // 显示诗句
      displaySimplePoems(poems);
      updateSimpleStats(poems);
    })
    .catch(error => {
      console.error('加载失败:', error);
      document.getElementById('poems-simple-list').innerHTML = 
        '<div class="error-simple">❌ 加载失败: ' + error.message + '</div>';
    });
}

// 显示诗句
function displaySimplePoems(poems) {
  const container = document.getElementById('poems-simple-list');

  if (poems.length === 0) {
    container.innerHTML = '<div class="no-poems">暂无诗句数据</div>';
    return;
  }

  let html = '';
  poems.forEach((poem, index) => {
    html += `
      <div class="poem-simple-item">
        <div class="poem-simple-content">
          <span class="poem-simple-text">${poem}</span>
          <span class="poem-simple-meta">#${index + 1} · ${poem.length}字</span>
        </div>
      </div>
    `;
  });

  container.innerHTML = html;
}

// 更新统计
function updateSimpleStats(poems) {
  const total = poems.length;

  document.querySelector('.subtitle').textContent = `共收录 ${total} 句优美诗句`;
  document.querySelectorAll('.stat-number')[0].textContent = total;
}

// 搜索功能
function simpleSearch() {
  const keyword = document.getElementById('search-simple-input').value.toLowerCase().trim();
  const allPoems = Array.from(document.querySelectorAll('.poem-simple-text'))
    .map(element => element.textContent);

  const container = document.getElementById('poems-simple-list');

  if (keyword === '') {
    // 显示所有诗句
    document.querySelectorAll('.poem-simple-item').forEach(item => {
      item.style.display = 'block';
    });
    return;
  }

  // 过滤显示
  document.querySelectorAll('.poem-simple-item').forEach((item, index) => {
    if (allPoems[index].toLowerCase().includes(keyword)) {
      item.style.display = 'block';
    } else {
      item.style.display = 'none';
    }
  });
}

// 导出功能
function simpleExportTxt() {
  const poems = Array.from(document.querySelectorAll('.poem-simple-text'))
    .map(element => element.textContent);

  const text = poems.map((poem, index) => `${index + 1}. ${poem}`).join('\n\n');
  downloadFile(text, '诗句列表.txt', 'text/plain');
}

function simpleExportJson() {
  const poems = Array.from(document.querySelectorAll('.poem-simple-text'))
    .map(element => element.textContent);

  const json = JSON.stringify(poems, null, 2);
  downloadFile(json, 'poems.json', 'application/json');
}

function downloadFile(content, filename, type) {
  const blob = new Blob([content], { type: type });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = filename;
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
}

// 页面加载后执行
document.addEventListener('DOMContentLoaded', function() {
  console.log('DOM加载完成，开始加载诗句...');
  loadSimplePoems();

  // 搜索框回车事件
  document.getElementById('search-simple-input').addEventListener('keypress', function(e) {
    if (e.key === 'Enter') {
      simpleSearch();
    }
  });
});
</script>

<style>
/* 基础样式 */
.poems-simple-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
}

.poems-simple-header {
  text-align: center;
  margin-bottom: 30px;
}

.poems-simple-header h1 {
  color: #2c3e50;
  margin-bottom: 10px;
  font-size: 2.2em;
}

.subtitle {
  color: #7f8c8d;
  font-size: 1.1em;
}

/* 统计信息 */
.poems-simple-stats {
  display: flex;
  justify-content: center;
  margin-bottom: 30px;
}

.stat {
  text-align: center;
  padding: 15px 25px;
  background: #f8f9fa;
  border-radius: 10px;
  min-width: 100px;
}

.stat-number {
  display: block;
  font-size: 1.8em;
  font-weight: bold;
  color: #3498db;
  margin-bottom: 5px;
}

.stat-label {
  font-size: 0.9em;
  color: #7f8c8d;
}

/* 搜索框 */
.search-simple-box {
  display: flex;
  gap: 10px;
  margin-bottom: 30px;
  justify-content: center;
}

.search-simple-box input {
  padding: 10px 15px;
  border: 2px solid #ddd;
  border-radius: 8px;
  font-size: 1em;
  min-width: 300px;
}

.search-simple-box input:focus {
  outline: none;
  border-color: #3498db;
}

.search-simple-box button {
  padding: 10px 20px;
  background: #3498db;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1em;
}

.search-simple-box button:hover {
  background: #2980b9;
}

/* 诗句列表 */
.poems-simple-list {
  min-height: 200px;
}

.loading-simple {
  text-align: center;
  padding: 40px;
  color: #7f8c8d;
  font-size: 1.1em;
}

.error-simple {
  text-align: center;
  padding: 40px;
  color: #e74c3c;
  font-size: 1.1em;
}

.no-poems {
  text-align: center;
  padding: 40px;
  color: #7f8c8d;
}

.poem-simple-item {
  background: white;
  border: 1px solid #e1e8ed;
  border-radius: 8px;
  margin-bottom: 15px;
  padding: 20px;
  transition: all 0.3s ease;
}

.poem-simple-item:hover {
  border-color: #3498db;
  box-shadow: 0 2px 8px rgba(52, 152, 219, 0.1);
}

.poem-simple-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

.poem-simple-text {
  flex: 1;
  font-size: 1.1em;
  line-height: 1.5;
  color: #2c3e50;
}

.poem-simple-meta {
  color: #7f8c8d;
  font-size: 0.9em;
  white-space: nowrap;
}

/* 导出按钮 */
.export-simple-buttons {
  text-align: center;
  margin-top: 30px;
  padding-top: 20px;
  border-top: 1px solid #e1e8ed;
  display: flex;
  justify-content: center;
  gap: 15px;
}

.export-simple-buttons button {
  padding: 12px 24px;
  background: #27ae60;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1em;
  transition: background 0.3s ease;
}

.export-simple-buttons button:hover {
  background: #219a52;
}

.export-simple-buttons button:first-child {
  background: #3498db;
}

.export-simple-buttons button:first-child:hover {
  background: #2980b9;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .poems-simple-container {
    padding: 15px;
  }

  .poems-simple-stats {
    gap: 20px;
  }

  .stat {
    padding: 10px 15px;
    min-width: 80px;
  }

  .search-simple-box {
    flex-direction: column;
    align-items: center;
  }

  .search-simple-box input {
    min-width: auto;
    width: 100%;
    max-width: 400px;
  }

  .poem-simple-content {
    flex-direction: column;
    align-items: flex-start;
    gap: 10px;
  }

  .export-simple-buttons {
    flex-direction: column;
    align-items: center;
  }

  .export-simple-buttons button {
    width: 100%;
    max-width: 300px;
  }
}
</style>