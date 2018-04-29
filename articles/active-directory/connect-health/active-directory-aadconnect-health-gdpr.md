---
title: Azure AD Connect Health i zasady zachowania poufności użytkownika | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano zasady zachowania poufności użytkownika z usługi Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: bf26e91308cfec0dc8ede20e683919b5764a4868
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Zasady zachowania poufności użytkownika i Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ten artykuł dotyczy usługi Azure AD Connect Health i użytkownika prywatności.  Aby uzyskać informacje dotyczące usługi Azure AD Connect i użytkownika prywatności, zobacz artykuł [tutaj](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="user-privacy-classification"></a>Klasyfikacja prywatności użytkownika
Azure AD Connect Health należące do **procesora danych** Kategoria klasyfikacji GDPR. Jako potok procesor danych z usługi udostępnia usługi przetwarzania danych i klucza użytkowników końcowych. Azure AD Connect Health nie generuje danych użytkownika i nie ma niezależnego kontroli nad jakie dane osobiste są zbierane i sposobie ich użycia. Pobieranie danych, agregacji, analizy i raportowania w programie Azure AD Connect Health są oparte na istniejących danych lokalnych. 

## <a name="data-retention-policy"></a>Zasady przechowywania danych
Azure AD Connect Health nie generować raporty, wykonywać analizy lub wgląd ponad 30 dni. W związku z tym Azure AD Connect Health nie przechowywania, przetwarzania lub zachować wszystkie dane poza 30 dni. Ten projekt jest zgodne z przepisami GDPR przepisy dotyczące zgodności ochrony prywatności firmy Microsoft, a zasady przechowywania danych usługi Azure AD. 

Serwery z aktywny **dane usługi kondycji nie jest aktualny** **błąd** alerty dotyczące ponad 30 dni kolejnych sugeruje, że żadne dane nie osiągnęła Connect Health podczas tego okresu. Te serwery będą wyłączone i nie jest wyświetlana w portalu Connect Health. Aby ponownie włączyć serwerów, należy odinstalować i [ponownie zainstalować agenta programu health](active-directory-aadconnect-health-agent-install.md). Należy pamiętać, że to nie ma zastosowania do **ostrzeżenia** tego samego typu alertu. Ostrzeżenia wskazują, że brakuje częściowe dane z serwera, który zostanie wyświetlony alert dla. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Wyłączanie zbierania danych i monitoring w programie Azure AD Connect Health
Azure AD Connect Health umożliwia zatrzymać zbieranie danych dla poszczególnych serwerów monitorowanych lub wystąpienie monitorowanej usługi. Na przykład można zatrzymać zbieranie danych dla poszczególnych serwerów usług AD FS (Active Directory Federation Services), które są monitorowane przy użyciu usługi Azure AD Connect Health. Można również zatrzymać zbieranie danych dla całego wystąpienia usług AD FS, która jest monitorowana przy użyciu usługi Azure AD Connect Health. Gdy wybierzesz to zrobić, odpowiadające serwery są usuwane z portalu Azure AD Connect Health po zatrzymaniu zbierania danych. 

>[!IMPORTANT]
> Musisz uprawnienia Administrator globalny usługi Azure AD lub roli współautora w RBAC delete monitorowane serwery z usługi Azure AD Connect Health.
>
> Usuwanie serwera lub wystąpienie usługi z usługi Azure AD Connect Health nie jest nieodwracalne. 

### <a name="what-to-expect"></a>Czego można oczekiwać?
Po zatrzymaniu zbierania danych i monitorowania dla monitorowanych pojedynczy serwer lub wystąpienie monitorowanej usługi, należy uwzględnić następujące informacje:

- Po usunięciu wystąpienia monitorowanej usługi, wystąpienie jest usuwane z usługi Azure AD Connect Health monitorowania listy usług w portalu. 
- Po usunięciu monitorowany serwer lub wystąpienie monitorowanej usługi agenta programu Health nie zostaje odinstalowany lub usunąć z serwerów. Agent kondycji został skonfigurowany z wysyłania danych do usługi Azure AD Connect Health. Należy ręcznie odinstalować agenta programu Health na wcześniej monitorowanych serwerach.
- Jeśli nie odinstalowano agenta programu Health przed wykonaniem tej czynności, na serwerach powiązanych z agentem programu Health mogą pojawić się zdarzenia błędów.
- Wszystkie dane należącego do wystąpienia monitorowanej usługi zostaną usunięte zgodnie z zasadami przechowywania danych programu Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Wyłączanie zbierania danych i monitorowania na monitorowanym serwerze
Zobacz [jak usuwanie serwera z usługi Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Wyłączanie zbierania danych i monitorowania dla wystąpienia usługi monitorowane
Zobacz [jak usunąć wystąpienie usługi z usługi Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Ponowne włączenie funkcji zbierania danych i monitoring w programie Azure AD Connect Health
Aby ponownie włączyć monitoring w programie Azure AD Connect Health do celów wcześniej usuniętych monitorowanej usługi, należy odinstalować i [ponownie zainstalować agenta programu health](active-directory-aadconnect-health-agent-install.md) na wszystkich serwerach.


## <a name="next-steps"></a>Kolejne kroki
* [Przejrzyj zasady Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [Azure AD Connect i zasady zachowania poufności użytkownika](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

