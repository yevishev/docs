---
sourcePath: ru/ydb/ydb-docs-core/ru/core/reference/ydb-sdk/recipes/session_pool_limit/index.md
---
# Установить размер пула сессий

{% include [work in progress message](../_includes/addition.md) %}

Размер пула сессий на клиенте влияет на потребление ресурсов (память, процессор) на серверной стороне {{ ydb-short-name }}.
Простая математика: если `1000` клиентов одной базы данных имеют по `1000` сессий, то на серверной стороне создается `100000` акторов (воркеров, исполнителей сессий). Если не лимитировать количество сессий на клиенте, то можно получить "задумчивый" кластер в полу-аварийном состоянии.
По умолчанию в {{ ydb-short-name }} SDK установлен лимит в `50` сессий.
Хорошая рекомендация - устанавливать лимит на количество сессий на клиенте в минимальное-необходимое для штатной работы клиентского приложения. Следует иметь в виду, что сессия однопоточная что на серверной стороне, что на клиентской. Соответственно, если для расчетной нагрузки приложению необходимо выполнять  `1000` одновременных запросов (`inflight`) в {{ ydb-short-name }} - значит следует установить лимит в `1000` сессий.
Тут надо отличать расчетный `RPS` (requests per second, запросов в секунду) и `inflight`. В первом случае это общее количество выполненных запросов к {{ ydb-short-name }} за `1` секунду. Например, для `RPS`=`10000` и средним `latency` (задержка исполнения запроса) в `100`мс достаточно установить лимит в `1000` сессий. То есть каждая сессия за расчетную секунду выполнит в среднем `10` последовательных запросов.

Ниже приведены примеры кода установки лимита на пул сессий в разных {{ ydb-short-name }} SDK

{% list tabs %}

- Go


  {% include [go.md](_includes/go.md) %}

- Java


  {% include [java.md](_includes/java.md) %}

{% endlist %}