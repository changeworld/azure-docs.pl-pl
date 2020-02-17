---
title: Śledzenie żądań przychodzących na platformie Azure Application Insights przy użyciu języka Python OpenCensus | Microsoft Docs
description: Monitoruj wywołania żądań dla aplikacji w języku Python za pośrednictwem języka Python OpenCensus.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 475ba601c61169f92eddd7f203b7fa34ed2e4916
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368253"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Śledź przychodzące żądania przy użyciu języka Python OpenCensus

Dane żądania przychodzącego są zbierane przy użyciu języka Python OpenCensus i różnych integracji. Śledź przychodzące dane żądania wysyłane do aplikacji sieci Web utworzonych na podstawie popularnych struktur sieci Web `django`, `flask` i `pyramid`. Dane są następnie wysyłane do Application Insights w obszarze Azure Monitor jako dane telemetryczne `requests`.

Najpierw Instrumentacja aplikacji w języku Python przy użyciu najnowszego [zestawu SDK języka Python OpenCensus](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Śledzenie aplikacji Django

1. Pobierz i zainstaluj `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-django/) i instrumentację aplikacji za pomocą `django` oprogramowania pośredniczącego. Żądania przychodzące wysyłane do aplikacji `django` będą śledzone.

2. Uwzględnij `opencensus.ext.django.middleware.OpencensusMiddleware` w pliku `settings.py` w `MIDDLEWARE`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Upewnij się, że AzureExporter jest prawidłowo skonfigurowany w `settings.py` w `OPENCENSUS`.

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

4. Możesz również dodać adresy URL do `settings.py` w obszarze `BLACKLIST_PATHS` dla żądań, które nie mają być śledzone.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Śledzenie aplikacji kolb

1. Pobierz i zainstaluj `opencensus-ext-flask` z [PyPI](https://pypi.org/project/opencensus-ext-flask/) i instrumentację aplikacji za pomocą `flask` oprogramowania pośredniczącego. Żądania przychodzące wysyłane do aplikacji `flask` będą śledzone.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Oprogramowanie pośredniczące `flask` można skonfigurować bezpośrednio w kodzie. W przypadku żądań z adresów URL, które nie mają być śledzone, Dodaj je do `BLACKLIST_PATHS`.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Śledzenie aplikacji ostrosłupa

1. Pobierz i zainstaluj `opencensus-ext-django` z [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) i instrumentację aplikacji za pomocą `pyramid` animacji. Żądania przychodzące wysyłane do aplikacji `pyramid` będą śledzone.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Animację `pyramid` można skonfigurować bezpośrednio w kodzie. W przypadku żądań z adresów URL, które nie mają być śledzone, Dodaj je do `BLACKLIST_PATHS`.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Następne kroki

* [Mapa aplikacji](../../azure-monitor/app/app-map.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
* [Wyszukiwanie](../../azure-monitor/app/diagnostic-search.md)
* [Zapytanie log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
