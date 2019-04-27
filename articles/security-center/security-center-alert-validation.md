---
title: Walidacja alertów w usłudze Azure Security Center | Microsoft Docs
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
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 009f5fe7243b8ce597c2be9f9c6874cdb56d103c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706063"
---
# <a name="alerts-validation-in-azure-security-center"></a>Walidacja alertów w usłudze Azure Security Center
Ten dokument zawiera informacje dotyczące sposobu weryfikacji systemu pod kątem prawidłowej konfiguracji alertów usługi Azure Security Center.

## <a name="what-are-security-alerts"></a>Czym są alerty zabezpieczeń?
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich (takich jak zapora i rozwiązania ochrony punktów końcowych), aby wykrywać zagrożenia i powiadamiać Cię o nich za pomocą alertów. Przeczytaj artykuł [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), aby uzyskać więcej informacji na temat alertów zabezpieczeń, i artykuł [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), aby dowiedzieć się więcej o różnych typach alertów.

## <a name="alert-validation"></a>Walidacja alertu
Po zainstalowaniu agenta usługi Security Center wykonaj poniższe kroki na komputerze, na którym ma znajdować się atakowany zasób dotyczący alertu:

1. Skopiuj plik wykonywalny (na przykład calc.exe) na pulpit komputera lub do wybranego katalogu.
2. Zmień nazwę tego pliku na **ASC_AlertTest_662jfi039N.exe**.
3. Otwórz wiersz polecenia, a następnie wykonaj ten plik przy użyciu argumentu (po prostu fałszywych argument nazwy), takich jak: *ASC_AlertTest_662jfi039N.exe -foo*
4. Zaczekaj od 5 do 10 minut, a następnie otwórz alerty usługi Security Center. Powinien znajdować się tam alert podobny do następującego:

    ![Walidacja alertu](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

Podczas przeglądania tego alertu upewnij się, że pole Inspekcja argumentów włączona ma wartość true. Jeśli wartość to false, musisz włączyć inspekcję argumentów wiersza polecenia. Tę opcję możesz włączyć za pomocą następującego polecenia:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> Aby wyświetlić pokaz działania tej funkcji, zobacz [Alert Validation in Azure Security Center (Walidacja alertu w usłudze Azure Security Center)](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center).

## <a name="see-also"></a>Zobacz także
Ten artykuł zawiera wprowadzenie do procesu walidacji alertów. Teraz, kiedy znasz już usługę Security Center, zapoznaj się z następującymi artykułami:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
