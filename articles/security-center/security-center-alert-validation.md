---
title: Alert sprawdzania poprawności (plik testu EICAR) w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Ten dokument zawiera informacje pomocne podczas walidowania alertów zabezpieczeń w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626299"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Walidacja alertu (plik testu EICAR) w usłudze Azure Security Center
Ten dokument zawiera informacje dotyczące sposobu weryfikacji systemu pod kątem prawidłowej konfiguracji alertów usługi Azure Security Center.

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Alerty to powiadomienia, generowane przez Centrum zabezpieczeń po wykryciu zagrożenia na zasoby. Jego priorytet i wyświetla alerty wraz z informacjami, służące do szybkiego analizowania problemu. Usługa Security Center zawiera także zalecenia dotyczące można sposobu wyeliminowania skutków ataku.
Aby uzyskać więcej informacji, zobacz [alertów zabezpieczeń w usłudze Azure Security Center](security-center-alerts-overview.md) i [reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Walidacja alertu

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Sprawdź poprawność alertu na maszynie Wirtualnej Windows <a name="validate-windows"></a>

Po usługi Security Center agent jest zainstalowany na komputerze wykonaj następujące kroki na komputerze, w którym mają być się atakowany zasób dotyczący alertu:

1. Skopiuj plik wykonywalny (na przykład **calc.exe**) na pulpit komputera lub inny katalog wygody i zmień jego jako **ASC_AlertTest_662jfi039N.exe**.
1. Otwórz wiersz polecenia, a następnie wykonaj ten plik przy użyciu argumentu (po prostu fałszywych argument nazwy), takich jak: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Alert jest podobny do [przykład](#alert-validate) poniżej powinien być wyświetlany:

> [!NOTE]
> Podczas przeglądania tego alertu testu dla Windows, upewnij się, pole **inspekcja argumentów włączona** jest **true**. Jeśli jest **false**, a następnie należy włączyć inspekcję argumentów wiersza polecenia. Aby ją włączyć, należy użyć następującego polecenia:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Sprawdź poprawność alert dla maszyny Wirtualnej systemu Linux <a name="validate-linux"></a>

Po usługi Security Center agent jest zainstalowany na komputerze wykonaj następujące kroki na komputerze, w którym mają być się atakowany zasób dotyczący alertu:
1. Skopiuj plik wykonywalny w dogodnym miejscu i zmień jej nazwę na **. / asc_alerttest_662jfi039n**, na przykład:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Otwórz wiersz polecenia, a następnie wykonaj ten plik:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Alert jest podobny do [przykład](#alert-validate) poniżej powinien być wyświetlany:

### Przykład alertu <a name="alert-validate"></a>

![Przykład Walidacja alertu](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Zobacz także
Ten artykuł zawiera wprowadzenie do procesu walidacji alertów. Teraz, kiedy znasz już usługę Security Center, zapoznaj się z następującymi artykułami:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
