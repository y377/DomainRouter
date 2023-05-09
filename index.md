---
layout: default
title: 域名分流
---
<div class="row"><h6>Last Build Time: {{ site.last_build_time | date: "%Y-%m-%d %H:%M:%S %z" }}</h6><div>
<div class="row pt-3 bg-light bg-gradient">
    <div class="col">
        <button type="button" class="btn btn-outline-primary" id="selectAllButton"><i class="bi bi-list-check">Select All</i></button>
        <ul class="list-group">
            {% for item in site.data.domain_list %}
            <li class="list-group-item">
                <input class="form-check-input me-1" type="checkbox" value="{{ item.contain_domain | join: '<br>'}}" id="{{ item.name }}Checkbox">
                <label class="form-check-label" for="{{ item.name }}Checkbox">{{ item.name }}<i class="bi bi-dash"></i><a href="https://{{ item.domain }}" class="link-offset-2 link-offset-3-hover link-underline-danger link-underline-opacity-0 link-underline-opacity-75-hover">{{ item.domain }}</a><i class="bi bi-dash opacity-50">{{ item.update_time }}</i></label>
            </li>
            {% endfor %}
        </ul>
    </div>
    <div id="copyArea" class="col">
        <h5>待复制区域 <button class="btn btn-danger" id="oneclickcopy">点击一键复制</button></h5>
        <textarea id="copyContent" style="height: auto; min-height: 300px;" rows="2" cols="50" readonly></textarea>
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
