# Управление базами данных

- [Получить список баз данных в кластере](#list-db).
- [Создать базу данных](#add-db).
- [Изменить параметры сортировки базы данных](#collation).
- [Удалить базу данных](#remove-db).

{% include [db-sql](../../_includes/mdb/mms/db-sql-limits.md) %}

## Получить список баз данных в кластере {#list-db}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ mms-name }}**.
  1. Нажмите на имя нужного кластера, затем выберите вкладку **Базы данных**.

- API

  Воспользуйтесь методом API [list](../api-ref/Database/list.md): передайте значение идентификатора требуемого кластера в параметре `clusterId` запроса.

  Чтобы узнать идентификатор кластера, [получите список кластеров в каталоге](cluster-list.md#list-clusters).

{% endlist %}

## Создать базу данных {#add-db}

В каждом кластере {{ mms-name }} вы можете создать неограниченное количество баз данных.

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ mms-name }}**.
  1. Нажмите на имя нужного кластера.
  1. Если владельцем новой базы данных должен стать еще не существующий пользователь, [создайте его](cluster-users.md#adduser).
  1. Выберите вкладку **Базы данных**.
  1. Нажмите кнопку **Добавить**.
  1. Введите имя базы данных и нажмите кнопку **Создать**.

      {% include [database-name-limits](../../_includes/mdb/mms/note-info-db-name-limits.md) %}

  1. [Выдайте роль `DB_OWNER`](grant.md) пользователю, который должен стать владельцем новой базы данных.

- Terraform

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [{#T}](./cluster-create.md).

    1. Добавьте к описанию кластера {{ mms-name }} блок `database`.

        ```hcl
        resource "yandex_mdb_sqlserver_cluster" "<имя кластера>" {
          ...
          database {
            name = "<имя базы>"
          }
        }
        ```

        {% include [database-name-limits](../../_includes/mdb/mms/note-info-db-name-limits.md) %}

    1. Добавьте блок `permission` к описанию пользователя, который должен стать владельцем новой базы данных:

        ```hcl
        resource "yandex_mdb_sqlserver_cluster" "<имя кластера>" {
          ...
          user {
            ...
            permission {
              database_name = "<имя базы данных>"
              roles         = ["OWNER"]
            }
            ...
          }
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-mms }}).

- API

  Воспользуйтесь методом API [create](../api-ref/Database/create.md) и передайте в запросе:
  - Идентификатор кластера, в котором вы хотите создать базу данных в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Имя базы данных в параметре `databaseSpec.name`.

      {% include [database-name-limits](../../_includes/mdb/mms/note-info-db-name-limits.md) %}

Чтобы узнать идентификатор кластера, [получите список кластеров в каталоге](cluster-list.md#list-clusters).

{% endlist %}

## Изменить параметры сортировки базы данных {#collation}

1. [Подключитесь](connect.md) к базе данных с помощью учетной записи ее владельца.
1. Выполните запрос `ALTER DATABASE` и укажите имя новых параметров сортировки.

{% note info %}

Запрос изменит параметры сортировки только для новых объектов БД. О том, как изменить параметры сортировки для существующих баз данных, читайте в [документации {{ MS }}](https://docs.microsoft.com/ru-ru/sql/relational-databases/collations/set-or-change-the-database-collation).

{% endnote %}

Подробнее см. в [документации {{ MS }}](https://docs.microsoft.com/ru-ru/sql/relational-databases/collations/set-or-change-the-database-collation).

## Удалить базу данных {#remove-db}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ mms-name }}**.
  1. Нажмите на имя нужного кластера и выберите вкладку **Базы данных**.
  1. Нажмите значок ![image](../../_assets/horizontal-ellipsis.svg) в строке нужной БД и выберите пункт **Удалить**.

- Terraform

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [{#T}](./cluster-create.md).

    1. Удалите из описания кластера {{ mms-name }} блок `database` с описанием базы данных.

    1. Удалите из описания пользователей блоки `permission` с полем `database_name`, указывающим на удаляемую базу.

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите удаление ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-mms }}).

- API

  Воспользуйтесь методом API [delete](../api-ref/Database/delete.md) и передайте в запросе:
  - Идентификатор кластера, в котором находится база данных в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  - Имя базы данных в параметре `databaseName`. Чтобы узнать имя базы данных, [получите список баз данных в кластере](#list-db).

{% endlist %}

{% note warning %}

Прежде чем создать новую базу с тем же именем, дождитесь завершения операции удаления, иначе будет восстановлена удаляемая база. Статус операции можно получить вместе со [списком операций в кластере](cluster-list.md#list-operations).

{% endnote %}
