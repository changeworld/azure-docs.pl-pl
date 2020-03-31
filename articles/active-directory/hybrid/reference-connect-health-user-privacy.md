---
title: Kondycja i prywatność użytkowników usługi Azure AD Connect | Dokumenty firmy Microsoft
description: W tym dokumencie opisano prywatność użytkowników za pomocą usługi Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58cddcde71e6e86b9abe07eb4200f13ad55ea659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253665"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Prywatność użytkowników i kondycja usługi Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ten artykuł dotyczy usługi Azure AD Connect Health i prywatności użytkowników.  Aby uzyskać informacje na temat usługi Azure AD Connect i prywatności użytkowników, zobacz artykuł [tutaj](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Klasyfikacja prywatności użytkowników
Usługa Azure AD Connect Health należy do kategorii **procesora danych** klasyfikacji RODO. Jako potok podmiotu przetwarzającego dane usługa świadczy usługi przetwarzania danych dla kluczowych partnerów i konsumentów końcowych. Usługa Azure AD Connect Health nie generuje danych użytkownika i nie ma niezależnej kontroli nad tym, jakie dane osobowe są zbierane i jak są używane. Pobieranie danych, agregacja, analiza i raportowanie w usłudze Azure AD Connect Health są oparte na istniejących danych lokalnych. 

## <a name="data-retention-policy"></a>Zasady przechowywania danych
Usługa Azure AD Connect Health nie generuje raportów, nie przeprowadza analizy ani nie dostarcza szczegółowych informacji po 30 dniach. W związku z tym usługi Azure AD Connect Health nie przechowuje, przetwarzać ani przechowywać żadnych danych poza 30 dni. Ten projekt jest zgodny z przepisami RODO, przepisami dotyczącymi zgodności z przepisami firmy Microsoft dotyczącymi prywatności i zasadami przechowywania danych usługi Azure AD. 

Serwery z aktywnymi **danymi usługi kondycji nie są aktualne** alerty o **błędach** przez ponad 30 kolejnych dni sugerują, że żadne dane nie osiągnęły Connect Health w tym przedziale czasu. Te serwery zostaną wyłączone i nie będą wyświetlane w portalu Connect Health. Aby ponownie włączyć serwery, należy odinstalować i [ponownie zainstalować agenta kondycji](how-to-connect-health-agent-install.md). Należy pamiętać, że nie dotyczy to **ostrzeżeń** o tym samym typie alertu. Ostrzeżenia wskazują, że na serwerze, na który użytkownik jest powiadamiany, brakuje danych częściowych. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Wyłączanie zbierania i monitorowania danych w usłudze Azure AD Connect Health
Usługa Azure AD Connect Health umożliwia zatrzymanie zbierania danych dla każdego monitorowanego serwera lub wystąpienia monitorowanej usługi. Na przykład można zatrzymać zbieranie danych dla poszczególnych serwerów ADFS (Active Directory Federation Services), które są monitorowane przy użyciu usługi Azure AD Connect Health. Można również zatrzymać zbieranie danych dla całego wystąpienia usługi ADFS, które jest monitorowane przy użyciu usługi Azure AD Connect Health. Gdy zdecydujesz się to zrobić, odpowiednie serwery są usuwane z portalu usługi Azure AD Connect Health, po zatrzymaniu zbierania danych. 

>[!IMPORTANT]
> Aby usunąć monitorowane serwery z usługi Azure AD Connect Health, potrzebujesz uprawnień administratora globalnego usługi Azure AD lub roli współautora w rbac.
>
> Usuwanie wystąpienia serwera lub usługi z usługi Azure AD Connect Health nie jest działaniem odwracalnym. 

### <a name="what-to-expect"></a>Czego się spodziewać?
W przypadku zatrzymania zbierania i monitorowania danych dla poszczególnych monitorowanych serwerów lub wystąpienia monitorowanej usługi należy zwrócić uwagę na następujące kwestie:

- Po usunięciu wystąpienia monitorowanej usługi wystąpienie jest usuwane z listy usługi monitorowania usługi Azure AD Connect Health w portalu. 
- Po usunięciu monitorowanego serwera lub wystąpienia monitorowanej usługi agent kondycji NIE jest odinstalowywana ani usuwana z serwerów. Agent kondycji jest skonfigurowany do wysyłania danych do usługi Azure AD Connect Health. Należy ręcznie odinstalować agenta kondycji na wcześniej monitorowanych serwerach.
- Jeśli agent kondycji nie został odinstalowany przed wykonaniem tego kroku, na serwerach związanych z agentem kondycji mogą wystąpić zdarzenia błędów.
- Wszystkie dane należące do wystąpienia monitorowanej usługi są usuwane zgodnie z zasadami przechowywania danych platformy Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Wyłączanie zbierania i monitorowania danych dla wystąpienia monitorowanej usługi
Zobacz, [jak usunąć wystąpienie usługi z usługi Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Wyłączanie zbierania i monitorowania danych dla monitorowanego serwera
Zobacz, [jak usunąć serwer z usługi Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Wyłącz zbieranie i monitorowanie danych dla wszystkich monitorowanych usług w usłudze Azure AD Connect Health
Usługa Azure AD Connect Health udostępnia również opcję zatrzymania zbierania danych **wszystkich** zarejestrowanych usług w dzierżawie. Przed podjęciem akcji zalecamy staranne rozważenie i pełne potwierdzenie wszystkich administratorów globalnych. Po rozpoczęciu procesu usługa Connect Health przestanie odbierać, przetwarzać i zgłaszać wszelkie dane wszystkich usług. Istniejące dane w usłudze Connect Health będą przechowywane przez okres nie dłużej niż 30 dni.
Jeśli chcesz zatrzymać zbieranie danych określonego serwera, wykonaj kroki w celu usunięcia określonych serwerów. Aby zatrzymać zbieranie danych w sposób dzierżawy, wykonaj następujące kroki, aby zatrzymać zbieranie danych i usunąć wszystkie usługi dzierżawy.

1. Kliknij ustawienia **ogólne** w konfiguracji w głównym bloku. 
2. Kliknij przycisk **Zatrzymaj zbieranie danych** w górnej części bloku. Inne opcje ustawień konfiguracji dzierżawy zostaną wyłączone po uruchomieniu procesu.  
 
   ![Zatrzymywać zbieranie danych](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Upewnij się, że lista wbudowanych usług, których dotyczy zatrzymanie kolekcji danych. 
4. Wprowadź dokładną nazwę dzierżawy, aby włączyć przycisk Akcji **Usuń**
5. Kliknij **przycisk Usuń,** aby wyzwolić usunięcie wszystkich usług. Connect Health przestanie odbierać, przetwarzać, zgłaszać wszelkie dane wysyłane z wbudowanych usług. Cały proces może potrwać do 24 godzin. Należy zauważyć, że ten krok nie jest odwracalny. 
6. Po zakończeniu procesu nie będzie już żadnych zarejestrowanych usług w Connect Health. 

   ![Po zatrzymaniu zbierania danych](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Ponowne włączanie zbierania i monitorowania danych w usłudze Azure AD Connect Health
Aby ponownie włączyć monitorowanie w usłudze Azure AD Connect Health dla wcześniej usuniętej monitorowaną usługą, należy odinstalować i [ponownie zainstalować agenta kondycji](how-to-connect-health-agent-install.md) na wszystkich serwerach.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Ponowne włączanie gromadzenia i monitorowania danych dla wszystkich monitorowanych usług

Zbieranie danych w obszarze dzierżawy można wznowić w usłudze Azure AD Connect Health. Przed podjęciem akcji zalecamy staranne rozważenie i pełne potwierdzenie wszystkich administratorów globalnych.

>[!IMPORTANT]
> Poniższe kroki będą dostępne po 24 godzinach wyłączania akcji.
> Po włączeniu zbierania danych przedstawione dane dotyczące wglądu i monitorowania w programie Connect Health nie będą wyświetlane żadne starsze dane zebrane wcześniej. 

1. Kliknij ustawienia **ogólne** w konfiguracji w głównym bloku. 
2. Kliknij przycisk **Włącz zbieranie danych** w górnej części bloku. 
 
   ![Włączanie zbierania danych](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Wprowadź dokładną nazwę dzierżawy, aby aktywować przycisk **Włącz.**
4. Kliknij przycisk **Włącz,** aby udzielić uprawnień do zbierania danych w usłudze Connect Health. Zmiana zostanie wkrótce zastosowana. 
5. Postępuj zgodnie z [procesem instalacji,](how-to-connect-health-agent-install.md) aby ponownie zainstalować agenta na serwerach, które mają być monitorowane, a usługi będą obecne w portalu.  


## <a name="next-steps"></a>Następne kroki
* [Zapoznaj się z zasadami zachowania poufności firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [Usługa Azure AD Connect i prywatność użytkowników](reference-connect-user-privacy.md)

