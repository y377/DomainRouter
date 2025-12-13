---
layout: default
title: 域名分流
description: 特别适和爱快软路由的域名分流
---

<a href="https://github.com/y377/DomainRouter" class="github-corner" aria-label="View source on GitHub">
  <svg width="80" height="80" viewBox="0 0 250 250"
    style="fill:#151513; color:#fff; position: absolute; top: 0; border: 0; right: 0;" aria-hidden="true">
    <path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path>
    <path
      d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2"
      fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path>
    <path
      d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z"
      fill="currentColor" class="octo-body"></path>
  </svg>
</a>

<div class="row">
  <p class="mb-2">Last Build Time: {{ site.time | date: "%Y-%m-%d %H:%M:%S" }}</p>
</div>

<div class="row pt-3 bg-light bg-gradient rounded-3 p-2">
  <div class="col-lg-6 col-md-6 col-sm-12">
    <div class="d-flex align-items-center gap-2 mb-2">
      <button type="button" class="btn btn-outline-primary" id="selectAllButton">
        <i class="bi bi-list-check"></i> <span id="selectAllText">Select All</span>
      </button>

      <input id="filterInput" class="form-control" placeholder="筛选（支持名称/域名）..." />
    </div>

    <ul class="list-group" id="domainList">
      {% for item in site.data.domain_list %}
      <li class="list-group-item d-flex align-items-start gap-2 domain-item"
          data-name="{{ item.name | escape }}"
          data-domain="{{ item.domain | escape }}">
        {% assign cd = item.contain_domain | default: empty %}
        <input class="form-check-input mt-1"
               type="checkbox"
               value="{{ cd | join: '\n' }}"
               id="{{ item.name | escape }}Checkbox">

        <label class="form-check-label w-100" for="{{ item.name | escape }}Checkbox">
          {% assign icon = item.icon | to_s | strip %}

          {% if icon != '' %}
            {%- comment -%}
            1) icon 是 HTML（<svg> / <i> / <span>...）→ 直接输出
            2) icon 像 assets/... 或 xx.svg/png/jpg → 当图片路径
            3) 否则当 Bootstrap Icons 的 class（bi bi-xxx）
            {%- endcomment -%}

            {% if icon startswith '<' %}
              <span class="icon-wrap">{{ icon }}</span>
            {% elsif icon contains '/' or icon contains '.' %}
              <img class="app-icon" src="{{ icon | relative_url }}" alt="{{ item.name | escape }}" loading="lazy">
            {% else %}
              <i class="{{ icon | replace_first: 'bi', 'bi fs-4' }} app-bi"></i>
            {% endif %}
          {% else %}
            <span class="app-icon app-icon--empty"></span>
          {% endif %}

          <span class="app-name">{{ item.name }}</span>
          <i class="bi bi-dash"></i>
          <a href="https://{{ item.domain }}" class="app-domain">{{ item.domain }}</a>
          {% if item.update_time %}
            <small class="app-updated"><i class="bi bi-dash"></i>{{ item.update_time }}</small>
          {% endif %}
        </label>
      </li>
      {% endfor %}
    </ul>
  </div>

  <div id="copyArea" class="col-lg-6 col-md-6 col-sm-12 mt-3 mt-md-0">
    <div class="d-flex align-items-center justify-content-between gap-2 mb-2">
      <button class="btn btn-danger" id="oneclickcopy">
        <i class="bi bi-clipboard-check"></i> 点击一键复制
      </button>
      <span class="text-secondary small" id="copyStatus"></span>
    </div>

    <textarea id="copyContent"
      style="min-height: 300px;"
      rows="10"
      class="form-control"
      readonly></textarea>
  </div>
</div>

<script>
(function () {
  const list = document.getElementById('domainList');
  const items = Array.from(list.querySelectorAll('.domain-item'));
  const checkboxes = items.map(li => li.querySelector('input[type="checkbox"]'));

  const copyContent = document.getElementById('copyContent');
  const copyStatus = document.getElementById('copyStatus');

  const selectAllButton = document.getElementById('selectAllButton');
  const selectAllText = document.getElementById('selectAllText');

  const filterInput = document.getElementById('filterInput');

  function getCheckedText() {
    // 把勾选项的 value（已是 \n 分隔）拼起来，去掉空行
    const parts = [];
    checkboxes.forEach(cb => {
      if (cb.checked) {
        const v = (cb.value || '').trim();
        if (v) parts.push(v);
      }
    });
    return parts.join('\n') + (parts.length ? '\n' : '');
  }

  function refreshCopyArea() {
    copyContent.value = getCheckedText();
    autoResizeTextArea();
  }

  function autoResizeTextArea() {
    copyContent.style.height = 'auto';
    copyContent.style.height = Math.max(copyContent.scrollHeight, 300) + 'px';
  }

  function saveCheckboxState(cb) {
    try { localStorage.setItem(cb.id, cb.checked ? 'true' : 'false'); } catch (e) {}
  }

  function restoreCheckboxState() {
    checkboxes.forEach(cb => {
      try {
        const status = localStorage.getItem(cb.id);
        if (status === 'true') cb.checked = true;
      } catch (e) {}
    });
  }

  function updateSelectAllText() {
    const visibleCheckboxes = checkboxes.filter(cb => cb.closest('.domain-item').style.display !== 'none');
    const allChecked = visibleCheckboxes.length > 0 && visibleCheckboxes.every(cb => cb.checked);
    selectAllText.textContent = allChecked ? 'Unselect All' : 'Select All';
  }

  async function copyToClipboard(text) {
    // 优先新 API，失败回退旧 API
    try {
      await navigator.clipboard.writeText(text);
      return true;
    } catch (e) {
      try {
        copyContent.focus();
        copyContent.select();
        return document.execCommand('copy');
      } catch (e2) {
        return false;
      }
    }
  }

  function showStatus(msg, ok=true) {
    copyStatus.textContent = msg;
    copyStatus.className = ok ? 'text-success small' : 'text-danger small';
    setTimeout(() => { copyStatus.textContent = ''; copyStatus.className = 'text-secondary small'; }, 1800);
  }

  // 1) 初始化：恢复勾选状态 + 刷新复制区
  restoreCheckboxState();
  refreshCopyArea();
  updateSelectAllText();

  // 2) checkbox change：保存状态 + 刷新复制区
  checkboxes.forEach(cb => {
    cb.addEventListener('change', () => {
      saveCheckboxState(cb);
      refreshCopyArea();
      updateSelectAllText();
    });
  });

  // 3) 全选/取消全选（仅对“当前可见”的项生效）
  selectAllButton.addEventListener('click', () => {
    const visibleCheckboxes = checkboxes.filter(cb => cb.closest('.domain-item').style.display !== 'none');
    const allChecked = visibleCheckboxes.length > 0 && visibleCheckboxes.every(cb => cb.checked);

    visibleCheckboxes.forEach(cb => {
      cb.checked = !allChecked;
      saveCheckboxState(cb);
    });

    refreshCopyArea();
    updateSelectAllText();
  });

  // 4) 复制按钮
  document.getElementById('oneclickcopy').addEventListener('click', async () => {
    const text = copyContent.value || '';
    if (!text.trim()) {
      showStatus('没有可复制内容（先勾选左侧项）', false);
      return;
    }
    const ok = await copyToClipboard(text);
    showStatus(ok ? '已复制到剪贴板 ✅' : '复制失败（请手动全选复制）', ok);
  });

  // 5) 过滤（名称/域名）
  filterInput.addEventListener('input', () => {
    const q = (filterInput.value || '').trim().toLowerCase();
    items.forEach(li => {
      const name = (li.dataset.name || '').toLowerCase();
      const domain = (li.dataset.domain || '').toLowerCase();
      const hit = !q || name.includes(q) || domain.includes(q);
      li.style.display = hit ? '' : 'none';
    });
    updateSelectAllText();
  });

  // 6) 窗口变化时，重新适配高度
  window.addEventListener('resize', autoResizeTextArea);
})();
</script>

<style>
/* GitHub corner */
.github-corner:hover .octo-arm { animation: octocat-wave 560ms ease-in-out }
@keyframes octocat-wave { 0%,100%{transform:rotate(0)} 20%,60%{transform:rotate(-25deg)} 40%,80%{transform:rotate(10deg)} }
@media (max-width:500px) { .github-corner:hover .octo-arm{animation:none} .github-corner .octo-arm{animation: octocat-wave 560ms ease-in-out} }

/* Icon + text */
.icon-wrap svg,
.icon-wrap i { vertical-align: -0.2em; margin-right: .35rem; }

.app-icon { width: 1.25rem; height: 1.25rem; object-fit: contain; vertical-align: -0.2rem; margin-right: .45rem; }
.app-icon--empty { display: inline-block; background: #e9ecef; border-radius: .35rem; }

.app-bi { vertical-align: -0.2rem; margin-right: .45rem; }

.app-name { font-weight: 600; margin-right: .5rem; }
.app-domain { color: inherit; text-decoration: none; }
.app-domain:hover { text-decoration: underline; }
.app-updated { color: #6c757d; margin-left: .25rem; }
</style>
