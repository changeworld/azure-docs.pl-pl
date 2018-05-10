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
ms.openlocfilehash: 5fedbac439636b56da217e7babd30820bce7b342
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
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

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Wyłączanie zbierania danych i monitorowania dla wystąpienia usługi monitorowane
Zobacz [jak usunąć wystąpienie usługi z usługi Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Wyłączanie zbierania danych i monitorowania na monitorowanym serwerze
Zobacz [jak usuwanie serwera z usługi Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Wyłączanie zbierania danych i monitorowania wszystkich monitorowanych usług w Azure AD Connect Health
Azure AD Connect Health udostępnia opcję, aby zatrzymać zbieranie danych z **wszystkie** zarejestrowanych usług w dzierżawie. Zaleca się staranne brany pod uwagę i pełne potwierdzenia wszyscy administratorzy globalni przed wykonaniem akcji. Po rozpoczęciu procesu, Connect Health usługa zostanie zatrzymana, otrzymywanie, przetwarzania i wszelkie dane wszystkich usług raportowania. Istniejące dane w usłudze kondycji połączenia zostaną zachowane w nie więcej niż 30 dni.
Jeśli chcesz zatrzymać zbieranie danych z określonego serwera, wykonaj kroki opisane w temacie usuwanie określonych serwerów. Aby zatrzymać zbieranie danych tenant-wise, wykonaj następujące kroki, aby zatrzymać zbieranie danych i usunąć wszystkie usługi dzierżawcy.

1.  Polecenie **ustawienia ogólne** w obszarze Konfiguracja w głównym bloku. 
2.  Polecenie **Zatrzymaj zbieranie danych** przycisk w górnej części bloku. Inne opcje ustawień konfiguracji dzierżawy zostanie wyłączona po rozpoczęciu procesu.  
 
 ![Zatrzymaj zbieranie danych](./media/active-directory-aadconnect-health-gdpr/gdpr4.png)
  
3.  Upewnij się, na liście usług został załadowany, których dotyczy zatrzymywania zbierania danych. 
4.  Wprowadź nazwę dokładne dzierżawy, aby włączyć **usunąć** przycisku akcji
5.  Polecenie **usunąć** do wyzwolenia usunięcie wszystkich usług. Połącz kondycji zostanie zatrzymana, odbierania, przetwarzanie, raportowanie danych wysyłanych z usługi został załadowany. Cały proces może potrwać do 24 godzin. Zwróć uwagę, że ten krok nie jest odwracalne. 
6.  Po zakończeniu procesu, nie zobaczysz żadnych zarejestrowanych usług w Connect Health więcej. 

 ![Po zatrzymaniu zbierania danych](./media/active-directory-aadconnect-health-gdpr/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Ponowne włączenie funkcji zbierania danych i monitoring w programie Azure AD Connect Health
Aby ponownie włączyć monitoring w programie Azure AD Connect Health do celów wcześniej usuniętych monitorowanej usługi, należy odinstalować i [ponownie zainstalować agenta programu health](active-directory-aadconnect-health-agent-install.md) na wszystkich serwerach.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Ponowne włączenie funkcji zbierania danych i monitorowania wszystkich monitorowanych usług

Zbieranie danych tenant-Wise można wznowić w Azure AD Connect Health. Zaleca się staranne brany pod uwagę i pełne potwierdzenia wszyscy administratorzy globalni przed wykonaniem akcji.

>[!IMPORTANT]
> Poniższe kroki będą dostępne po 24 godzinach wyłączyć akcji.
> Po włączeniu zbierania danych, przedstawione szczegółowe dane i dane w Connect Health monitorowania nie będą widoczne wszystkie starsze dane zebrane przed. 

1.  Polecenie **ustawienia ogólne** w obszarze Konfiguracja w głównym bloku. 
2.  Polecenie **włączyć zbieranie danych** przycisk w górnej części bloku. 
 
 ![Włączanie zbierania danych](./media/active-directory-aadconnect-health-gdpr/gdpr6.png)
 
3.  Wprowadź nazwę dokładne dzierżawy, aby aktywować **włączyć** przycisku.
4.  Polecenie **włączyć** przycisk, aby udzielić uprawnień zbierania danych w usłudze Connect Health. Zmiany zostaną zastosowane wkrótce. 
5.  Postępuj zgodnie z [proces instalacji](active-directory-aadconnect-health-agent-install.md) ponownie zainstalować agenta na serwerach do monitorowania i usługi będą znajdować się w portalu.  


## <a name="next-steps"></a>Kolejne kroki
* [Przejrzyj zasady Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [Azure AD Connect i zasady zachowania poufności użytkownika](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

