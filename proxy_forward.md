已完成持久化，当前 17891 转发已由 launchd 托管。

  结果：

  1. 已创建并加载用户级服务：ai.openclaw.proxy-forward
  2. 服务策略：开机自启（RunAtLoad）+ 异常自动拉起（KeepAlive）
  3. 监听已改为更安全的 127.0.0.1:17891，转发到 192.168.110.31:7890
  4. 验证通过：
      - 容器可达：container_to_17891_ok
      - 渠道探测：telegram_probe_ok true、discord_probe_ok true

  本次相关仓库改动仍保留并生效：

  - docker-compose.yml
  - .docker-shims/src/infra/net/global-proxy-bootstrap.mjs
  - .docker-shims/src/infra/outbound/send-deps.js

  常用管理命令：

  - 查看状态：launchctl print gui/$(id -u)/ai.openclaw.proxy-forward
  - 重启服务：launchctl kickstart -k gui/$(id -u)/ai.openclaw.proxy-forward
  - 停止并卸载：launchctl bootout gui/$(id -u)/ai.openclaw.proxy-forward
