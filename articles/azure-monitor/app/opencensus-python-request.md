---
title: Śledzenie żądań przychodzących w usłudze Azure Application Insights z programem OpenCensus Python | Dokumenty firmy Microsoft
description: Monitorowanie wywołań żądań dla aplikacji Python za pośrednictwem OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669951"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Śledzenie przychodzących żądań za pomocą języka OpenCensus Python

Przychodzące dane żądania są zbierane przy użyciu OpenCensus Python i jego różnych integracji. Śledzenie przychodzących danych żądań wysyłanych do aplikacji internetowych `django` `flask` zbudowanych na podstawie popularnych struktur internetowych i `pyramid`. Dane są następnie wysyłane do usługi `requests` Application Insights w obszarze Azure Monitor jako dane telemetryczne.

Po pierwsze, instrument aplikacji Python z najnowszym [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Śledzenie aplikacji Django

1. Pobierz i `opencensus-ext-django` zainstaluj z [PyPI](https://pypi.org/project/opencensus-ext-django/) i `django` instrument aplikacji z oprogramowaniem pośredniczącym. Przychodzące żądania `django` wysyłane do aplikacji będą śledzone.

2. Dołącz `opencensus.ext.django.middleware.OpencensusMiddleware` do `settings.py` pliku `MIDDLEWARE`w obszarze .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Upewnij się, że usługa AzureExporter `settings.py` `OPENCENSUS`jest poprawnie skonfigurowana w obszarze .

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

4. Możesz również dodać adresy URL do `settings.py` pod `BLACKLIST_PATHS` żądaniami, których nie chcesz śledzić.

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

## <a name="tracking-flask-applications"></a>Śledzenie aplikacji kolby

1. Pobierz i `opencensus-ext-flask` zainstaluj z [PyPI](https://pypi.org/project/opencensus-ext-flask/) i `flask` instrument aplikacji z oprogramowaniem pośredniczącym. Przychodzące żądania `flask` wysyłane do aplikacji będą śledzone.

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

2. Oprogramowanie pośredniczące `flask` można skonfigurować bezpośrednio w kodzie. W przypadku żądań od adresów URL, których `BLACKLIST_PATHS`nie chcesz śledzić, dodaj je do pliku .

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

## <a name="tracking-pyramid-applications"></a>Śledzenie aplikacji Pyramid

1. Pobierz i `opencensus-ext-django` zainstaluj z [pypi](https://pypi.org/project/opencensus-ext-pyramid/) i `pyramid` instrument aplikacji z animacji. Przychodzące żądania `pyramid` wysyłane do aplikacji będą śledzone.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Animację można `pyramid` skonfigurować bezpośrednio w kodzie. W przypadku żądań od adresów URL, których `BLACKLIST_PATHS`nie chcesz śledzić, dodaj je do pliku .

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
* [Wyszukaj](../../azure-monitor/app/diagnostic-search.md)
* [Kwerenda dziennika (Analizy)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnostyka transakcji](../../azure-monitor/app/transaction-diagnostics.md)
