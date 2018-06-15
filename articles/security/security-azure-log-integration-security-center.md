---
title: Integracja dziennika Azure z Centrum zabezpieczeń | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać Praca z integracji dziennika alerty Centrum zabezpieczeń Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29138497"
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Jak uzyskać alerty Centrum zabezpieczeń Azure dziennika integracji

Ten artykuł zawiera kroki wymagane do włączenia usługi integracji dziennika Azure ściągania zabezpieczeń informacji o alertach generowanych przez Centrum zabezpieczeń Azure. Należy pomyślnie wykonać kroki opisane w [wprowadzenie](security-azure-log-integration-get-started.md) artykuł przed wykonaniem kroków w tym artykule.

## <a name="detailed-steps"></a>Szczegółowe procedury

Poniższe kroki będzie utworzyć wymagane usługi Azure Active Directory nazwy głównej usługi i przypisać zasady usługi uprawnienia odczytu do subskrypcji:
1. Otwórz wiersz polecenia i przejdź do **c:\Program Files\Microsoft Azure dziennika integracji**
2. Uruchom polecenie``azlog createazureid``

    To polecenie wyświetla monit o podanie logowania do systemu Azure. Polecenie utworzy [Azure Active Directory — nazwy głównej usługi](../active-directory/develop/active-directory-application-objects.md) w Azure dzierżaw AD, które subskrypcji platformy Azure, w których zalogowany użytkownik jest administratorem, administratora współpracującego lub właściciela hosta. Polecenie zakończy się niepowodzeniem, jeśli tylko użytkownik-Gość w dzierżawie usługi Azure AD jest zalogowanego użytkownika. Uwierzytelnianie na platformie Azure odbywa się za pośrednictwem usługi Azure Active Directory (AD). Tworzenie nazwy głównej usługi integracji Azlog tworzy tożsamość usługi Azure AD, które będzie mieć dostęp do odczytu z subskrypcji platformy Azure.

3. Następnie zostanie uruchamiania poleceń, które przypisuje dostęp czytelnika subskrypcji do nazwy głównej usługi został utworzony. Jeśli identyfikator subskrypcji nie jest określony, polecenie będzie podejmować można przypisać rolę czytelnika główną usługi do wszystkie subskrypcje, do której masz dostęp. </br></br>
``azlog authorize <SubscriptionID>`` </br> na przykład </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Zobaczysz ostrzeżenia, jeśli od razu po uruchomieniu polecenia autoryzacji ```createazureid``` polecenia. Brak niektórych opóźnienia między podczas tworzenia konta usługi Azure AD i jeśli konto nie jest dostępny do użycia. Jeśli Poczekaj około 60 sekund po uruchomieniu ```createazureid``` polecenie do uruchomienia polecenia autoryzacji, a następnie nie powinna być widoczna tych ostrzeżeń.

4. Sprawdź następujące foldery, aby potwierdzić, że pliki JSON dziennika inspekcji są dostępne:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Sprawdź następujące foldery, aby upewnić się, że alerty Centrum zabezpieczeń, istnieją w nich:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Jeśli wystąpiły problemy podczas instalacji i konfiguracji, otwórz [żądania obsługi](/azure-supportability/how-to-create-azure-support-request.md), wybierz pozycję **integracji dziennika** jako usługa żądania pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat integracji dziennika Azure, można znaleźć w następujących dokumentach:

* [Microsoft Azure dziennika integracji Azure dzienników](https://www.microsoft.com/download/details.aspx?id=53324) — Centrum pobierania, aby uzyskać szczegółowe informacje, wymagania systemowe i zainstalować instrukcje dotyczące integracji dzienników Azure.
* [Wprowadzenie do integracji dzienników Azure](security-azure-log-integration-overview.md) — ten dokument stanowi wprowadzenie do integracji dzienników Azure, jego kluczowych możliwości i jak działa.
* [Czynności konfiguracyjnych partnera](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) — ten wpis w blogu przedstawiono sposób konfigurowania integracji dzienników Azure do pracy z rozwiązań partnerskich Splunk HP ArcSight i IBM QRadar.
* [Dzienników Azure — często zadawane pytania (FAQ) integracji](security-azure-log-integration-faq.md) — to często zadawane pytania dotyczące odpowiedzi na pytania dotyczące integracji dzienników Azure.
* [Nowe funkcje diagnostyki Azure i dzienników inspekcji platformy Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) — ten wpis w blogu stanowi wprowadzenie do dzienników inspekcji platformy Azure i inne funkcje, które ułatwiają uzyskać wgląd w funkcjonowanie zasobów platformy Azure.
