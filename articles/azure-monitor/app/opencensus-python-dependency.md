---
title: Śledzenie zależności w usłudze Azure Application Insights przy użyciu języka Python OpenCensus | Microsoft Docs
description: Monitoruj wywołania zależności dla aplikacji w języku Python za pośrednictwem języka Python OpenCensus.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669934"
---
# <a name="track-dependencies-with-opencensus-python"></a>Śledzenie zależności przy użyciu języka Python OpenCensus

Zależność jest składnikiem zewnętrznym, który jest wywoływany przez aplikację. Dane zależności są zbierane przy użyciu języka Python OpenCensus i różnych integracji. Dane są następnie wysyłane do Application Insights w obszarze Azure Monitor jako dane telemetryczne `dependencies`.

Najpierw Instrumentacja aplikacji w języku Python przy użyciu najnowszego [zestawu SDK języka Python OpenCensus](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Zależności w procesie

OpenCensus Python SDK dla Azure Monitor umożliwia wysyłanie danych telemetrycznych zależności w procesie (informacji i logiki, które są wykonywane w aplikacji). Zależności w procesie będą mieć `type` pole jako `INPROC` w analizie.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Zależności z integracją "requests"

Śledź żądania wychodzące z integracją OpenCensus `requests`.

Pobierz i zainstaluj `opencensus-ext-requests` z [PyPI](https://pypi.org/project/opencensus-ext-requests/) i Dodaj je do integracji śledzenia. Żądania wysyłane za pomocą biblioteki [żądań](https://pypi.org/project/requests/) języka Python będą śledzone.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Zależności z integracją "httplib"

Śledź żądania wychodzące z integracją OpenCensus `httplib`.

Pobierz i zainstaluj `opencensus-ext-httplib` z [PyPI](https://pypi.org/project/opencensus-ext-httplib/) i Dodaj je do integracji śledzenia. Żądania wysyłane przy użyciu [protokołu HTTP. Client](https://docs.python.org/3.7/library/http.client.html) dla python3 lub [httplib](https://docs.python.org/2/library/httplib.html) dla python2 będą śledzone.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Zależności z integracją "Django"

Śledź wychodzące żądania Django z integracją z usługą OpenCensus `django`.

Pobierz i zainstaluj `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-django/) i Dodaj następujący wiersz do sekcji `MIDDLEWARE` w pliku `settings.py` Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Można podać dodatkową konfigurację, aby [dowiedzieć się, jak uzyskać pełne](https://github.com/census-instrumentation/opencensus-python#customization) odwołanie.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Zależności z integracją "MySQL"

Śledź zależności MYSQL z integracją OpenCensus `mysql`. Ta integracja obsługuje bibliotekę [łącznika MySQL](https://pypi.org/project/mysql-connector-python/) .

Pobierz i zainstaluj `opencensus-ext-mysql` z [PyPI](https://pypi.org/project/opencensus-ext-mysql/) i Dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Zależności z integracją "pymysql"

Śledź zależności PyMySQL z integracją z usługą OpenCensus `pymysql`.

Pobierz i zainstaluj `opencensus-ext-pymysql` z [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) i Dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Zależności z integracją "PostgreSQL"

Śledź zależności PostgreSQL z integracją z usługą OpenCensus `postgresql`. Ta integracja obsługuje bibliotekę [psycopg2](https://pypi.org/project/psycopg2/) .

Pobierz i zainstaluj `opencensus-ext-postgresql` z [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) i Dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Zależności z integracją "pymongo"

Śledź zależności MongoDB z integracją z usługą OpenCensus `pymongo`. Ta integracja obsługuje bibliotekę [pymongo](https://pypi.org/project/pymongo/) .

Pobierz i zainstaluj `opencensus-ext-pymongo` z [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) i Dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Zależności z integracją "SQLAlchemy"

Śledź zależności za pomocą SQLAlchemy przy użyciu integracji OpenCensus `sqlalchemy`. Ta integracja śledzi użycie pakietu [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) niezależnie od podstawowej bazy danych.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Następne kroki

* [Mapa aplikacji](../../azure-monitor/app/app-map.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
* [Wyszukiwanie](../../azure-monitor/app/diagnostic-search.md)
* [Zapytanie log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
