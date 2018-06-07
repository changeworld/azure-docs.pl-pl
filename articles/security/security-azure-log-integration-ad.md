---
title: Integracja dziennika Azure z dzienników inspekcji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługę Azure dziennika integracji oraz integrowanie dzienniki z dzienników inspekcji platformy Azure
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
ms.date: 05/25/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 201e34cf9e9c917f1f79964facce136e7c0c2fd3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642465"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integracja dzienników inspekcji usługi Azure Active Directory

Zdarzenia inspekcji w usłudze Azure Active Directory (Azure AD) pomaga zidentyfikować uprzywilejowanych akcji, które wystąpiły w usłudze Azure Active Directory. Można wyświetlić typy zdarzeń, które można śledzić, przeglądając [zdarzenia raportów inspekcji usługi Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Dzienniki inspekcji kroki integracji Azure Active Directory

> [!NOTE]
> Przed podjęciem próby kroki opisane w tym artykule, należy przejrzeć [wprowadzenie](security-azure-log-integration-get-started.md) artykułu i wykonaj odpowiednie kroki istnieje.

1. Otwórz wiersz polecenia i uruchom to polecenie:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Uruchom następujące polecenie: 
 
   ``azlog createazureid``

   To polecenie wyświetla monit o podanie logowania do systemu Azure. Polecenie następnie tworzy usługi Azure Active Directory nazwy głównej usługi w dzierżaw usługi Azure AD, które subskrypcji platformy Azure, w których zalogowany użytkownik jest administratora, administratora współpracującego lub właściciela hosta. Polecenie zakończy się niepowodzeniem, jeśli tylko użytkownik-Gość w dzierżawie usługi Azure AD jest zalogowanego użytkownika. Uwierzytelnianie na platformie Azure odbywa się za pośrednictwem usługi Azure AD. Tworzenie nazwy głównej usługi integracji dziennika Azure tworzy tożsamości usługi Azure AD, która uzyskuje dostęp do odczytu z subskrypcji platformy Azure.

3. Uruchom następujące polecenie, aby zapewnić swojego identyfikatora dzierżawcy. Musisz być członkiem roli administratora dzierżawy o uruchomienie polecenia.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Przykład:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Sprawdź następujące foldery, aby upewnić się, że pliki JSON dziennika inspekcji usługi Azure Active Directory są tworzone w nich:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Poniżej film wideo przedstawia kroki omówione w tym artykule:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Aby uzyskać szczegółowe instrukcje dotyczące przełączania informacji w plikach JSON do informacji o zabezpieczeniach i zdarzeń systemu zarządzania (SIEM) skontaktuj się z dostawcą SIEM.

Społeczność pomoc jest dostępna za pośrednictwem [Forum MSDN integracji dziennika Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Forum to umożliwia użytkownikom w społeczności integracji dziennika Azure do obsługi siebie z pytania, odpowiedzi, porady i wskazówki. Ponadto zespół Azure dziennika integracji monitoruje tym forum i pomaga zawsze, gdy mogą go.

Można również otworzyć [żądania obsługi](../azure-supportability/how-to-create-azure-support-request.md). Wybierz **integracji dziennika** jako usługa żądania pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat integracji dziennika Azure, zobacz:

* [Microsoft Azure dziennika integracji Azure dzienników](https://www.microsoft.com/download/details.aspx?id=53324): strony Centrum pobierania ten zapewnia szczegółowe informacje, wymagania systemowe i instrukcje dotyczące instalacji integracji dziennika Azure.
* [Wprowadzenie do integracji dziennika Azure](security-azure-log-integration-overview.md): w tym artykule przedstawiono integracji dziennika Azure, jego kluczowych możliwości i jak działa.
* [Często zadawane pytania Azure dziennika integracji](security-azure-log-integration-faq.md): w tym artykule odpowiedzi na pytania dotyczące integracji dziennika Azure.
* [Nowe funkcje diagnostyki Azure i Azure dzienniki inspekcji](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): ten wpis w blogu stanowi wprowadzenie do dzienników inspekcji platformy Azure i inne funkcje, które ułatwiają uzyskać wgląd w funkcjonowanie zasobów platformy Azure.
