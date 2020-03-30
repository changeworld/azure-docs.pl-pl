---
title: Osadź klienta analizatora internetowego | Dokumenty firmy Microsoft
description: W tym artykule dowiesz się, jak osadzić klienta JavaScript analizatora internetowego w aplikacji.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74896388"
---
# <a name="embed-the-internet-analyzer-client"></a>Osadzanie klienta analizatora internetowego

W tym artykule pokazano, jak osadzić klienta JavaScript w aplikacji. Instalacja tego klienta jest niezbędna do uruchamiania testów i otrzymywania analizy karty wyników. **Klient JavaScript specyficzny dla profilu jest dostarczany po skonfigurowaniu pierwszego testu.** W tym miejscu można kontynuować dodawanie lub usuwanie testów do tego profilu bez konieczności osadzania nowego skryptu. Aby uzyskać więcej informacji na temat analizatora internetowego, zobacz [omówienie](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Analizator internetowy wymaga dostępu do platformy Azure i innych usług firmy Microsoft do poprawnego działania. Przed osadzeniem `fpc.msedge.net` klienta należy zezwolić na dostęp do sieci i wszelkie wstępnie skonfigurowane adresy URL punktów końcowych (widoczne za pośrednictwem [interfejsu wiersza](internet-analyzer-cli.md)polecenia).

## <a name="find-the-client-script-url"></a>Znajdowanie adresu URL skryptu klienta

Adres URL skryptu można znaleźć za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure po skonfigurowaniu testu. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu analizatora internetowego](internet-analyzer-create-test-portal.md).

Sposób 1. W witrynie Azure portal użyj [tego łącza,](https://aka.ms/InternetAnalyzerPreviewPortal) aby otworzyć stronę portalu w wersji zapoznawczej dla analizatora internetowego platformy Azure. Przejdź do profilu analizatora internetowego, aby wyświetlić adres URL skryptu, przechodząc do **ustawień > konfiguracji**.

Sposób 2. Korzystając z interfejsu wiersza polecenia platformy Azure, sprawdź `scriptFileUri` właściwość.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Dane klienta

Skrypt jest generowany specjalnie dla twojego profilu i testów. Po załadowaniu skrypt zostanie wykonany z 2-sekundowym opóźnieniem. Najpierw kontaktuje się z usługą Analizatora Internet, aby pobrać listę punktów końcowych skonfigurowanych w testach. Następnie uruchamia pomiary i przesyła wyniki z czasem z powrotem do usługi Analizatora Internet.

## <a name="client-examples"></a>Przykłady klientów

Te przykłady pokazują kilka podstawowych metod osadzania klienta JavaScript na stronie internetowej lub aplikacji. Używamy `0bfcb32638b44927935b9df86dcfe397` jako przykładowy identyfikator profilu dla adresu URL skryptu.

### <a name="run-on-page-load"></a>Uruchom przy wczytyniu strony
Najprostszą metodą jest użycie znacznika skryptu wewnątrz bloku metatagu. Ten tag wykona skrypt raz na załadowanie strony.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane [pytania dotyczące analizatora internetowego](internet-analyzer-faq.md)
