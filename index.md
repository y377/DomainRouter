---
layout: default
title: 域名分流
description: 特别适和爱快软路由的域名分流
---
<a href="https://github.com/y377/DomainRouter" class="github-corner" aria-label="View source on GitHub"><svg width="80" height="80" viewBox="0 0 250 250" style="fill:#151513; color:#fff; position: absolute; top: 0; border: 0; right: 0;" aria-hidden="true"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path></svg></a><style>.github-corner:hover .octo-arm{animation:octocat-wave 560ms ease-in-out}@keyframes octocat-wave{0%,100%{transform:rotate(0)}20%,60%{transform:rotate(-25deg)}40%,80%{transform:rotate(10deg)}}@media (max-width:500px){.github-corner:hover .octo-arm{animation:none}.github-corner .octo-arm{animation:octocat-wave 560ms ease-in-out}}</style>
<div class="row">
    <p>Last Build Time: {{ site.time | date: "%Y-%m-%d %H:%M:%S" }}</p>
</div>
<div class="row pt-3 bg-light bg-gradient">
    <div class="col-lg-6 col-md-6 col-sm-12">
        <button type="button" class="btn btn-outline-primary" id="selectAllButton"><i class="bi bi-list-check">Select All</i></button>
        <ul class="list-group">
            {% for item in site.data.domain_list %}
            <li class="list-group-item">
                <input class="form-check-input align-middle me-1 fs-5" type="checkbox" value="{{ item.contain_domain | join: '<br>'}}" id="{{ item.name }}Checkbox">
                <label class="form-check-label align-middle" for="{{ item.name }}Checkbox">{{ item.icon | replace_first: "bi", "bi fs-3" }}  {{ item.name }}<i class="bi bi-dash"></i><a href="https://{{ item.domain }}" class="link-offset-2 link-offset-3-hover link-underline-danger link-underline-opacity-0 link-underline-opacity-75-hover">{{ item.domain }}</a><i class="bi bi-dash opacity-50">{{ item.update_time }}</i></label>
            </li>
            {% endfor %}
        </ul>
    </div>
    <div id="copyArea" class="col-lg-6 col-md-6 col-sm-12">
        <h5><button class="btn btn-danger" id="oneclickcopy">点击一键复制</button></h5>
        <textarea id="copyContent" style="height: auto; min-height: 300px;" rows="2" class="col-12" readonly></textarea>
    </div>
</div>
<script type="text/javascript">
// 获取复选框元素和待复制区域元素
const checkboxes = Array.from(document.querySelectorAll('input[type="checkbox"]'));
const copyContent = document.getElementById('copyContent');
const selectAllButton = document.getElementById('selectAllButton');

// 定义变量保存手动调整的高度
let textareaHeight = '';

// 为每个复选框添加事件监听器
checkboxes.forEach((checkbox) => {
    checkbox.addEventListener('change', () => {
        if (checkbox.checked) {
            // 复选框被选中时，将文本添加到待复制区域
            const text = checkbox.value;
            const formattedText = text.replace(/<br>/g, '\n');
            copyContent.value += formattedText + '\n';
        } else {
            // 复选框被取消选中时，从待复制区域删除文本
            const text = checkbox.value;
            const formattedText = text.replace(/<br>/g, '\n');
            copyContent.value = copyContent.value.replace(formattedText + '\n', '');
        }

        // 调整高度
        autoAdjustTextAreaHeight();
    });

    // 页面加载时恢复勾选状态
    const checkedStatus = localStorage.getItem(checkbox.id);
    checkbox.checked = checkedStatus === 'true';
});

// 全选按钮点击事件
let selectAllFlag = false; // 标记全选状态
selectAllButton.addEventListener('click', () => {
    if (!selectAllFlag) {
        checkboxes.forEach((checkbox) => {
            checkbox.checked = true;
        });
    } else {
        checkboxes.forEach((checkbox) => {
            checkbox.checked = false;
        });
    }

    selectAllFlag = !selectAllFlag; // 切换全选状态

    // 清空待复制区域内容
    copyContent.value = '';

    // 将内容插入待复制区域
    checkboxes.forEach((checkbox) => {
        if (checkbox.checked) {
            const text = checkbox.value;
            const formattedText = text.replace(/<br>/g, '\n');
            copyContent.value += formattedText + '\n';
        }
    });

    // 调整高度
    autoAdjustTextAreaHeight();
});

// 调整初始高度和监听复选框变化事件
function autoAdjustTextAreaHeight() {
    copyContent.style.height = 'auto';
    copyContent.style.height = copyContent.scrollHeight + 'px';

    // 保存手动调整的高度，仅在高度增加时保存
    if (copyContent.style.height > textareaHeight) {
        textareaHeight = copyContent.style.height;
    }
}

// 监听用户手动调整 <textarea> 的高度事件，并恢复高度
copyContent.addEventListener('input', () => {
    copyContent.style.height = textareaHeight;
});

// 允许待复制区域文本编辑
copyContent.readOnly = false;
// 创建一键复制按钮
const copyButton = document.querySelector('#copyArea button');
const textarea = document.getElementById('copyContent');

copyButton.addEventListener('click', () => {
    textarea.select();
    document.execCommand('copy');
    console.log('文本已成功复制到剪贴板');
});
</script>
