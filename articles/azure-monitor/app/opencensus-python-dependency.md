---
title: Śledzenie zależności w usłudze Azure Application Insights za pomocą programu OpenCensus Python | Dokumenty firmy Microsoft
description: Monitorowanie wywołań zależności dla aplikacji Python za pośrednictwem OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669934"
---
# <a name="track-dependencies-with-opencensus-python"></a>Śledzenie zależności za pomocą języka OpenCensus Python

Zależność jest zewnętrznym składnikiem, który jest wywoływany przez aplikację. Dane zależności są zbierane przy użyciu OpenCensus Python i jego różnych integracji. Dane są następnie wysyłane do usługi `dependencies` Application Insights w obszarze Azure Monitor jako dane telemetryczne.

Po pierwsze, instrument aplikacji Python z najnowszym [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Zależności w trakcie procesu

OpenCensus Python SDK for Azure Monitor umożliwia wysyłanie danych telemetrycznych zależności "w toku" (informacje i logika, które występują w aplikacji). Zależności w procesie będą `type` miały `INPROC` pole, jak w analizie.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Zależności z integracją "żądań"

Śledź swoje wychodzące żądania za `requests` pomocą integracji OpenCensus.

Pobierz i `opencensus-ext-requests` zainstaluj z [PyPI](https://pypi.org/project/opencensus-ext-requests/) i dodaj go do integracji śledzenia. Żądania wysyłane przy użyciu biblioteki [żądań](https://pypi.org/project/requests/) języka Python będą śledzone.

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

Śledź swoje wychodzące żądania `httplib` dzięki integracji OpenCensus.

Pobierz i `opencensus-ext-httplib` zainstaluj z [PyPI](https://pypi.org/project/opencensus-ext-httplib/) i dodaj go do integracji śledzenia. Żądania wysyłane przy użyciu [http.client](https://docs.python.org/3.7/library/http.client.html) dla Python3 lub [httplib](https://docs.python.org/2/library/httplib.html) dla Python2 będą śledzone.

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

## <a name="dependencies-with-django-integration"></a>Zależności z integracją "django"

Śledź swoje wychodzące żądania Django `django` dzięki integracji OpenCensus.

Pobierz i `opencensus-ext-django` zainstaluj z [PyPI](https://pypi.org/project/opencensus-ext-django/) i `MIDDLEWARE` dodaj następujący wiersz `settings.py` do sekcji w pliku Django.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Dodatkowa konfiguracja może być dostarczona, [odczytywanie dostosowań](https://github.com/census-instrumentation/opencensus-python#customization) dla pełnego odniesienia.

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

## <a name="dependencies-with-mysql-integration"></a>Zależności z integracją "mysql"

Śledź swoje zależności MYSQL z integracją OpenCensus. `mysql` Ta integracja obsługuje bibliotekę [łączników mysql.](https://pypi.org/project/mysql-connector-python/)

Pobierz i `opencensus-ext-mysql` zainstaluj z [pypi](https://pypi.org/project/opencensus-ext-mysql/) i dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Zależności z integracją "pymysql"

Śledź swoje zależności PyMySQL z integracją OpenCensus. `pymysql`

Pobierz i `opencensus-ext-pymysql` zainstaluj z [pypi](https://pypi.org/project/opencensus-ext-pymysql/) i dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Zależności z integracją "postgresql"

Śledź swoje zależności PostgreSQL z integracji OpenCensus. `postgresql` Integracja ta obsługuje bibliotekę [psycopg2.](https://pypi.org/project/psycopg2/)

Pobierz i `opencensus-ext-postgresql` zainstaluj z [pypi](https://pypi.org/project/opencensus-ext-postgresql/) i dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Zależności z integracją "pymongo"

Śledź swoje zależności MongoDB za pomocą `pymongo` integracji OpenCensus. Integracja ta obsługuje bibliotekę [pymongo.](https://pypi.org/project/pymongo/)

Pobierz i `opencensus-ext-pymongo` zainstaluj z [pypi](https://pypi.org/project/opencensus-ext-pymongo/) i dodaj następujące wiersze do kodu.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Zależności z integracją "sqlalchemy"

Śledzenie zależności przy użyciu SQLAlchemia przy `sqlalchemy` użyciu integracji OpenCensus. Ta integracja śledzi użycie pakietu [sqlalchemy,](https://pypi.org/project/SQLAlchemy/) niezależnie od podstawowej bazy danych.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Następne kroki

* [Mapa aplikacji](../../azure-monitor/app/app-map.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
* [Wyszukaj](../../azure-monitor/app/diagnostic-search.md)
* [Kwerenda dziennika (Analizy)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
