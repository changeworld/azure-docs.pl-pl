---
title: Azure Log Integration z dziennikami inspekcji Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak zainstalować usługę Azure Log Integration i zintegrować dzienniki z dzienników inspekcji platformy Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0820ca227a4d0e8c71ed3f35cd8fa2841163d38f
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727644"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrowanie dzienników inspekcji Azure Active Directory

Zdarzenia inspekcji Azure Active Directory (Azure AD) pomagają identyfikować uprzywilejowane akcje, które wystąpiły w Azure Active Directory. Możesz wyświetlić typy zdarzeń, które można śledzić, przeglądając [Azure Active Directory zdarzenia raportów inspekcji](../../active-directory/reports-monitoring/concept-audit-logs.md).


>[!IMPORTANT]
> Funkcja integracja z usługą Azure log zostanie zaniechana przez 06/15/2019. Pliki do pobrania AzLog zostały wyłączone w dniu 27 czerwca 2018. Aby uzyskać wskazówki na temat tego, co należy zrobić, przejrzyj temat publikowanie [za pomocą usługi Azure monitor w celu integracji z narzędziami Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Kroki umożliwiające integrację dzienników inspekcji Azure Active Directory

> [!NOTE]
> Przed podjęciem próby wykonania kroków opisanych w tym artykule należy zapoznać się z artykułem [wprowadzenie](azure-log-integration-get-started.md) i wykonać odpowiednie kroki.

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Uruchom następujące polecenie: 
 
   ``azlog createazureid``

   To polecenie umożliwia wyświetlanie danych logowania na platformie Azure. Następnie polecenie Azure Active Directory tworzy nazwę główną usługi w dzierżawach usługi Azure AD, która hostuje subskrypcje platformy Azure, w których zalogowany użytkownik jest administratorem, współadministratorem lub właścicielem. Polecenie zakończy się niepowodzeniem, Jeśli zalogowany użytkownik jest tylko użytkownikiem gościa w dzierżawie usługi Azure AD. Uwierzytelnianie na platformie Azure odbywa się za pomocą usługi Azure AD. Utworzenie nazwy głównej usługi dla Azure Log Integration powoduje utworzenie tożsamości usługi Azure AD mającej dostęp do odczytu z subskrypcji platformy Azure.

3. Uruchom następujące polecenie, aby podać swój identyfikator dzierżawy. Aby uruchomić polecenie, musisz być członkiem roli administratora dzierżawy.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Przykład:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Sprawdź następujące foldery, aby upewnić się, że pliki JSON dziennika inspekcji Azure Active Directory są w nich tworzone:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

Poniższy film wideo przedstawia kroki opisane w tym artykule:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Aby uzyskać szczegółowe instrukcje dotyczące umieszczania informacji w plikach JSON w systemie zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), skontaktuj się z dostawcą usługi SIEM.

Pomoc dla społeczności jest dostępna na [Forum Azure log Integration MSDN](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Na tym forum użytkownicy Azure Log Integration mogą obsługiwać siebie nawzajem przy użyciu pytań, odpowiedzi, porad i lew. Ponadto zespół Azure Log Integration monitoruje ten forum i pomaga w każdej sytuacji.

Możesz również otworzyć [żądanie pomocy technicznej](../../azure-supportability/how-to-create-azure-support-request.md). Wybierz pozycję **integracja dzienników** jako usługę, dla której chcesz uzyskać pomoc techniczną.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Azure Log Integration, zobacz:

* [Microsoft Azure log Integration dla dzienników platformy Azure](https://www.microsoft.com/download/details.aspx?id=53324): Ta strona Centrum pobierania zawiera szczegółowe informacje, wymagania systemowe i instrukcje dotyczące instalacji Azure Log Integration.
* [Wprowadzenie do Azure log Integration](azure-log-integration-overview.md): W tym artykule przedstawiono, jak Azure Log Integration, jej najważniejszych możliwości i jak działa.
* [Azure log Integration często zadawane pytania](azure-log-integration-faq.md): W tym artykule znajdują się odpowiedzi na pytania dotyczące Azure Log Integration.
* [Nowe funkcje dla Diagnostyka Azure i dzienników inspekcji platformy Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Ten wpis w blogu przedstawia dzienniki inspekcji platformy Azure i inne funkcje, które ułatwiają wgląd w operacje zasobów platformy Azure.
