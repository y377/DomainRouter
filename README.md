# 域名分流

这是一个基于 Jekyll 构建的域名分流项目。它允许用户在网页端直接操作，并且专业用户可以提交更多未包含的域名。

## 未来路线图

[] 使用本地Linux机器定时检测，当前[页面](https://www.abdd.net/)使用的是韩国甲骨文云服务器检测的，然后还需要我手动编辑；正确的是本地机器检测无法访问的网站，然后同步到GitHub。
[] 添加GitHub Action，将本地的机器检测到的玉面自动填入`_data/domain_list.yml`文件，然后自动编译。

## 提交新的域名

如果您是专业用户，并且希望提交新的域名以进行分流，请按照以下步骤操作：

1. 打开 `_data/domain_list.yml` 文件。
2. 在文件末尾添加一个新的域名条目，按照以下格式：

   ```yml
   - name: 域名名称
     domain: 域名地址
     contain_domain:
       - 子域名1
       - 子域名2
       - 子域名3
     update_time: YYYY-MM-DD
     ```
3. 提交您的更改，并创建一个拉取请求。
我们将审查您的提交并将其纳入域名分流列表中。

反馈和问题
如果您遇到任何问题或有任何反馈，请随时在本项目的 Issues 页面提交问题。

感谢您的贡献！

# DomainRouter
This is a domain routing project built with Jekyll. It allows users to operate directly on the web interface, and professional users can submit additional domains that are not included.

Submitting a New Domain
If you are a professional user and would like to submit a new domain for routing, please follow these steps:

Open the _data/domain_list.yml file.

Add a new domain entry at the end of the file using the following format:

```yml
- name: Domain Name
  domain: Domain Address
  contain_domain:
    - Subdomain 1
    - Subdomain 2
    - Subdomain 3
  update_time: YYYY-MM-DD
```
Replace the placeholders with the actual values for the name, domain address, subdomains, and update date.

Submit your changes and create a pull request.

We will review your submission and include it in the domain routing list.

Feedback and Issues
If you encounter any issues or have any feedback, please feel free to submit them on the Issues page of this project.

Thank you for your contribution!


