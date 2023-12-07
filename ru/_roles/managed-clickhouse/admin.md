Роль `managed-clickhouse.admin` предоставляет полный доступ к кластерам ClickHouse.

Пользователи с этой ролью могут создавать, изменять, удалять, запускать и останавливать кластеры ClickHouse, управлять доступом к кластерам, читать и сохранять информацию в базах данных, а также просматривать информацию о кластерах, логах их работы и квотах.

Включает разрешения, предоставляемые ролью `managed-clickhouse.editor`.

Для создания кластеров ClickHouse дополнительно необходима роль `vpc.user`.