# Обзор способов управления доступом в {{ objstorage-name }}

В {{ objstorage-name }} реализовано несколько взаимосвязанных механизмов для управления доступом:
* [{{ iam-full-name }} ({{ iam-short-name }})](#iam).
* [Список управления доступом (ACL)](#acl).
* [Политика доступа (bucket policy)](#policy).
* [Публичный доступ](#anonymous).
* [{{ sts-name }}](#sts).
* [Подписанные (pre-signed) URL](#pre-signed).

На схеме показана взаимосвязь механизмов управления доступом в {{ objstorage-name }}.

![access-scheme](../../_assets/storage/access-scheme.svg)

Алгоритм проверок:

1. _IAM_ и _ACL бакета_:
    * Если запрос прошел проверку _IAM_ **или** _ACL бакета_, проверяется включена ли _политика доступа бакета_.
    * Если запрос не прошел проверки _IAM_ **и** _ACL бакета_, проверяется включен ли _публичный доступ_ к бакету.
1. _Публичный доступ_:
    * Если публичный доступ на выполнение действия открыт, проверяется включена ли _политика доступа бакета_.
    * Если публичный доступ на выполнение действия закрыт, то применяется проверка доступа через _ACL объекта_.
1. _Политика доступа бакета_:
    * Если политика доступа включена:
      1. Если запрос подошел хотя бы под одно из правил `Deny` политики бакета, то применяется проверка доступа через _ACL объекта_.
      1. Если запрос подошел хотя бы под одно из правил `Allow` политики бакета, проверяется осуществлен ли доступ через _{{ sts-name }}_.
      1. Если запрос не подошел ни под одно из правил политики бакета, то применяется проверка доступа через _ACL объекта_.
    * Если политика доступа не включена, проверяется осуществлен ли доступ через _{{ sts-name }}_.
1. _{{ sts-name }}_:
    * Если запрос осуществлен с помощью {{ sts-name }}:
      1. Если запрос подошел хотя бы под одно из правил `Deny` политики для временного ключа, то применяется проверка доступа через _ACL объекта_.
      1. Если запрос подошел хотя бы под одно из правил `Allow` политики для временного ключа, доступ будет разрешен.
      1. Если запрос не подошел ни под одно из правил политики для временного ключа, то применяется проверка доступа через _ACL объекта_.
    * Если запрос осуществлен напрямую, доступ будет разрешен.
1. _ACL объекта_:
    * Если запрос прошел проверку _ACL объекта_, доступ будет разрешен.
    * Если запрос не прошел проверку _ACL объекта_, доступ будет запрещен.

## {{ iam-name }} {#iam}

[{{ iam-name }}](./index.md) — основной способ управления доступом в {{ yandex-cloud }} с помощью назначения ролей. Позволяет базово разграничить доступы, подробнее см. [{#T}](./index.md#roles-list).

Получатели доступа: 
* аккаунт на Яндексе;
* [сервисный аккаунт](../../iam/concepts/users/service-accounts.md);
* [федеративный пользователь](../../iam/concepts/federations.md);
* [группа пользователей](../../organization/operations/manage-groups.md);
* [системная группа](../../iam/concepts/access-control/system-group.md).

Доступ выдается на [облако](../../resource-manager/concepts/resources-hierarchy.md#cloud), [каталог](../../resource-manager/concepts/resources-hierarchy.md#folder) или [бакет](../concepts/bucket.md).

## Список управления доступом (ACL) {#acl}

[Список управления доступом (ACL)](./acl.md) — перечень разрешений на выполнение действий, хранящийся непосредственно в {{ objstorage-name }}. Позволяет базово разграничить доступы. Разрешения ACL для бакетов и объектов отличаются, подробнее см. [{#T}](./acl.md#permissions-types).

{% note info %}

Если у вас нет необходимости разграничивать доступ к конкретным объектам, рекомендуем использовать {{ iam-name }}.

{% endnote %}

Получатели доступа: 
* аккаунт на Яндексе;
* [сервисный аккаунт](../../iam/concepts/users/service-accounts.md);
* [федеративный пользователь](../../iam/concepts/federations.md);
* [группа пользователей](../../organization/operations/manage-groups.md);
* [системная группа](../../iam/concepts/access-control/system-group.md).

Доступ выдается на [бакет](../concepts/bucket.md) или [объект](../concepts/object.md).

## Политика доступа (bucket policy) {#policy}

[Политика доступа (bucket policy)](./policy.md) — перечень правил, запрещающих или разрешающих [действия](../s3/api-ref/policy/actions.md) при выполнении определенных [условий](../s3/api-ref/policy/conditions.md). Позволяет гранулярно разграничить доступы к бакетам, объектам и группам объектов.

Получатели доступа: 
* аккаунт на Яндексе;
* [сервисный аккаунт](../../iam/concepts/users/service-accounts.md);
* [федеративный пользователь](../../iam/concepts/federations.md);
* анонимный пользователь.

Доступ выдается на [бакет](../concepts/bucket.md), [объект](../concepts/object.md) или группу объектов.

## Публичный доступ {#anonymous}

[Публичный доступ](./public-access.md) — разрешения на доступ к чтению объектов, списка объектов и настроек бакета для анонимных пользователей.

Доступ выдается на [бакет](../concepts/bucket.md).

{% include [public-access-warning](../../_includes/storage/security/public-access-warning.md) %}

## {{ sts-name }} {#sts}

{% include [sts-preview](../../_includes/iam/sts-preview.md) %}

[{{ sts-name }}](./sts.md) — компонент сервиса {{ iam-name }} для получения временных ключей доступа, совместимых с [AWS S3 API](../s3/index.md).

С помощью временных ключей вы можете гранулярно разграничить доступы в бакеты для множества пользователей, используя для этого всего один сервисный аккаунт.

## Подписанные (pre-signed) URL {#pre-signed}

[Подписанные (pre-signed) URL](./pre-signed-urls.md) — способ предоставления анонимным пользователям временного доступа к определенным действиям в {{ objstorage-name }} с помощью URL, содержащих в своих параметрах данные для авторизации запроса.

Доступ выдается на [бакет](../concepts/bucket.md) или [объект](../concepts/object.md).

### См. также {#see-also}

* [{#T}](../operations/buckets/iam-access.md)
* [{#T}](../operations/buckets/edit-acl.md)
* [{#T}](../operations/objects/edit-acl.md)
* [{#T}](../operations/buckets/policy.md)
* [{#T}](../operations/buckets/bucket-availability.md)
* [{#T}](../operations/buckets/create-sts-key.md)