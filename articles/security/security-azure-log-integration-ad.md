---
title: Usługa Azure Log Integration z dziennikami inspekcji usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować usługę Azure Log Integration i integrowanie dzienników z dziennikami inspekcji platformy Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: db065c78008e47326155e9e2b3a0f65031ec4cd9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478433"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrowanie usługi Azure Active Directory z dziennikami inspekcji

Zdarzenia inspekcji w usłudze Azure Active Directory (Azure AD) pomoże zidentyfikować uprzywilejowanych akcji, które wystąpiły w usłudze Azure Active Directory. Można wyświetlić typy zdarzeń, które można śledzić, sprawdzając [zdarzenia raportu inspekcji usługi Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> Funkcja integracji dziennika Azure zostaną wycofane 06/01/2019 r. Pliki do pobrania AzLog zostały wyłączone w dniu 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu wpis [użycia w usłudze Azure monitor do integracji z narzędziami rozwiązania SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Dzienniki inspekcji czynności, aby zintegrować usługę Azure Active Directory

> [!NOTE]
> Przed rozpoczęciem kroków opisanych w tym artykule, należy przejrzeć [wprowadzenie](security-azure-log-integration-get-started.md) i wykonaj odpowiednie kroki istnieje.

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Uruchom następujące polecenie: 
 
   ``azlog createazureid``

   To polecenie wyświetli monit o podanie logowania do systemu Azure. Polecenie utworzy usługi Azure Active Directory nazwy głównej usługi w dzierżawach usługi Azure AD, obsługujące subskrypcji platformy Azure, w których zalogowanego użytkownika jest administrator, administrator współpracujący lub właściciela. Polecenie zakończy się niepowodzeniem, jeśli użytkownik zalogowany jest tylko użytkownik-Gość w dzierżawie usługi Azure AD. Uwierzytelnianie na platformie Azure odbywa się za pośrednictwem usługi Azure AD. Tworzenie nazwy głównej usługi Azure Log Integration tworzy tożsamości usługi Azure AD, która otrzymuje dostęp do odczytu z subskrypcji platformy Azure.

3. Uruchom następujące polecenie, aby zapewnić identyfikatora dzierżawy. Musisz być członkiem roli administratora dzierżawy do uruchomienia polecenia.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Przykład:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Sprawdź następujące foldery, aby upewnić się, że pliki JSON dziennika inspekcji usługi Azure Active Directory są tworzone w nich:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Poniższy film wideo przedstawia kroki omówione w tym artykule:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Aby uzyskać szczegółowe instrukcje zapewniania informacji w plikach JSON security information and event management (SIEM) systemu skontaktuj się z dostawcą rozwiązania SIEM.

Pomoc społeczności jest dostępna za pośrednictwem [forum MSDN dotyczącym usługi Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). To forum temu inni członkowie społeczności integracji dzienników platformy Azure do obsługi innych za pomocą pytań, odpowiedzi, porady i wskazówki. Ponadto zespół Azure Log Integration monitoruje to forum i pomaga w każdym przypadku, gdy jest to możliwe.

Możesz również otworzyć [żądania pomocy technicznej](../azure-supportability/how-to-create-azure-support-request.md). Wybierz **integracji dzienników** jako usługę, dla której zażądano pomocy technicznej.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat integracji dzienników platformy Azure, zobacz:

* [Microsoft Azure Log Integration dzienników platformy Azure](https://www.microsoft.com/download/details.aspx?id=53324): Ta strona Centrum pobierania udostępnia szczegółowe informacje, wymagania systemowe i instrukcje instalacji integracji dzienników Azure.
* [Wprowadzenie do integracji dziennika Azure](security-azure-log-integration-overview.md): Ten artykuł stanowi wprowadzenie do integracji dzienników platformy Azure, jego kluczowych możliwości i jak to działa.
* [Integracja dzienników platformy Azure — często zadawane pytania](security-azure-log-integration-faq.md): Ten artykuł zawiera odpowiedzi na pytania dotyczące usługi Azure Log Integration.
* [Dzienniki inspekcji nowe funkcje dla usługi Azure Diagnostics i Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Ten wpis w blogu zawiera wprowadzenie do dzienników inspekcji platformy Azure i inne funkcje, które pomagają uzyskać wgląd w operacje zasobów platformy Azure.
