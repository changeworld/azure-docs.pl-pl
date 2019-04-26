---
title: Azure AD Connect Health i ochrony prywatności użytkowników | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano rozwiązania prywatność użytkownika za pomocą usługi Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27e5ae48a8194c05a19e5164ee2cc6be68967a62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386246"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Prywatność użytkownika i program Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>W tym artykule dotyczy usługi Azure AD Connect Health i użytkownika ochrony prywatności.  Instrukcje dotyczące usługi Azure AD Connect i użytkownika ochrony prywatności, zobacz artykuł [tutaj](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Klasyfikacja prywatność użytkownika
Program Azure AD Connect Health uczciwy **przetwarzający dane** Kategoria klasyfikacji RODO. Jako potok przetwarzający dane usługa zapewnia usług przetwarzania danych dla klucza partnerów i użytkowników końcowych. Program Azure AD Connect Health nie generuje dane użytkownika i nie ma niezależnego kontroli nad jakie dane osobowe są zbierane i sposobie ich użycia. Pobieranie danych, agregacji, analizy i raportowania w programie Azure AD Connect Health są oparte na istniejących danych w środowisku lokalnym. 

## <a name="data-retention-policy"></a>Zasady przechowywania danych
Program Azure AD Connect Health nie generować raporty, przeprowadzanie analiz lub szczegółowymi po upływie 30 dni. W związku z tym program Azure AD Connect Health nie przechowujące, przetwarzające lub przechowywać żadnych danych, po upływie 30 dni. Ten projekt jest zgodna z rozporządzenia RODO, przepisy dotyczące zgodności ochrony prywatności firmy Microsoft i zasady przechowywania danych usługi Azure AD. 

Serwery z aktywnymi **dane usługi kondycji nie są na bieżąco** **błąd** alerty dla ponad 30 dni z rzędu sugeruje, że żadne dane nie osiągnęła Connect Health tego okresu. Te serwery będą wyłączone i nie są wyświetlane w portalu programu Connect Health. Aby ponownie włączyć serwerów, należy odinstalować i [ponownej instalacji agenta programu health](how-to-connect-health-agent-install.md). Należy pamiętać, że nie ma to zastosowania do **ostrzeżenia** przy użyciu tego samego typu alertu. Ostrzeżenia wskazuje, że brakuje częściowe dane z serwera, który zostanie wyświetlony alert dla. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Wyłączanie zbierania danych i monitorowania w Azure AD Connect Health
Program Azure AD Connect Health umożliwia zatrzymać zbieranie danych dla poszczególnych serwerów monitorowanych lub wystąpienie monitorowanej usługi. Na przykład możesz zatrzymać zbieranie danych dla poszczególnych serwerów usług AD FS (Active Directory Federation Services), które są monitorowane przy użyciu programu Azure AD Connect Health. Można je również zatrzymać zbieranie danych dla całego wystąpienia usług AD FS, która jest monitorowana przy użyciu programu Azure AD Connect Health. Jeśli zdecydujesz się zrobić, odpowiadające serwery zostaną usunięte z portalu programu Azure AD Connect Health po zatrzymaniu zbierania danych. 

>[!IMPORTANT]
> Musisz uprawnienia administratora globalnego usługi Azure AD lub rolę współautora RBAC delete monitorowany serwerów z programu Azure AD Connect Health.
>
> Usuwanie serwera lub wystąpienie usługi z usługi Azure AD Connect Health nie jest nieodwracalne. 

### <a name="what-to-expect"></a>Czego można oczekiwać?
Zatrzymanie zbierania danych i monitorowania dla poszczególnych serwerów monitorowanych lub wystąpienie monitorowanej usługi, pamiętaj o następujących kwestiach:

- Po usunięciu wystąpienie monitorowanej usługi wystąpienie zostanie usunięte z programu Azure AD Connect Health monitorowania listy usług w portalu. 
- Jeśli usuniesz monitorowanego serwera lub wystąpienie monitorowanej usługi agenta programu Health nie zostaje odinstalowany lub usunięte z serwerów. Agent kondycji jest skonfigurowany z wysyłania danych do usługi Azure AD Connect Health. Należy ręcznie odinstalować agenta programu Health na wcześniej monitorowanych serwerach.
- Jeśli nie odinstalowano agenta programu Health przed wykonaniem tego kroku, możesz widzieć zdarzenia dotyczące błędów na serwerach powiązanych z agentem programu Health.
- Wszystkie dane, należącego do wystąpienia monitorowanej usługi zostaną usunięte zgodnie z zasadami przechowywania danych platformy Microsoft.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Wyłączanie zbierania danych i monitorowania dla wystąpienia usługi monitorowane
Zobacz [jak usuwanie wystąpienia usługi Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Wyłączanie zbierania danych i monitorowania dla monitorowanego serwera
Zobacz [jak usunąć serwer z programu Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Wyłączanie zbierania danych i monitorowania wszystkich monitorowanych usług w usłudze Azure AD Connect Health
Program Azure AD Connect Health udostępnia opcję, aby zatrzymać zbieranie danych z **wszystkich** zarejestrowane usługi w dzierżawie. Firma Microsoft zaleca szczególną uwagę i pełne potwierdzenia wszyscy administratorzy globalni przed podjęciem działań. Po rozpoczęciu procesu usługi Connect Health przestanie odbierania, przetwarzania i zgłasza żadnych danych Twoich usług. Istniejące dane w usłudze programu Connect Health zostaną zachowane, nie więcej niż 30 dni.
Jeśli chcesz zatrzymać zbieranie danych z określonego serwera, wykonaj poniższe kroki należy wykonać na usunięcie określonych serwerów. Aby zatrzymać zbieranie danych tenant-wise, wykonaj następujące kroki, aby zatrzymać zbieranie danych i Usuń wszystkie usługi dzierżawy.

1. Kliknij pozycję **ustawienia ogólne** w obszarze Konfiguracja w głównym bloku. 
2. Kliknij pozycję **Zatrzymaj zbieranie danych** przycisk u góry bloku. Inne opcje ustawień konfiguracji dzierżawy zostanie wyłączona, gdy rozpoczyna się proces.  
 
   ![Zatrzymaj zbieranie danych](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Upewnij się, na liście usług dołączono, które mają wpływ zatrzymywanie zbierania danych. 
4. Wprowadź nazwę dzierżawy dokładnie umożliwiające **Usuń** przycisk akcji
5. Kliknij pozycję **Usuń** do wyzwolenia usunięcie wszystkich usług. Connect Health zostanie zatrzymane odbierania, przetwarzania, zgłasza żadnych danych wysyłanych z usługi dołączone. Cały proces może potrwać do 24 godzin. Należy zauważyć, że ten krok nie jest nieodwracalna. 
6. Po zakończeniu procesu, nie zobaczysz żadnych zarejestrowanych usług w programie Connect Health więcej. 

   ![Po zatrzymaniu zbierania danych](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Ponowne włączenie zbierania danych i monitorowania w Azure AD Connect Health
Aby ponownie włączyć monitorowania w Azure AD Connect Health dla usuniętego wcześniej monitorowanej usługi, należy odinstalować i [ponownej instalacji agenta programu health](how-to-connect-health-agent-install.md) na wszystkich serwerach.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Ponowne włączenie zbierania danych i monitorowania wszystkich monitorowanych usług

Zbieranie danych tenant-Wise może być wznowione w usłudze Azure AD Connect Health. Firma Microsoft zaleca szczególną uwagę i pełne potwierdzenia wszyscy administratorzy globalni przed podjęciem działań.

>[!IMPORTANT]
> Poniższe kroki będą dostępne po 24 godziny, wyłącz akcji.
> Po włączeniu zbierania danych prezentowanych wglądu i monitorowania danych w programie Connect Health nie będą widoczne wszystkie starsze dane zebrane przed. 

1. Kliknij pozycję **ustawienia ogólne** w obszarze Konfiguracja w głównym bloku. 
2. Kliknij pozycję **włączyć zbierania danych** przycisk u góry bloku. 
 
   ![Włączanie zbierania danych](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Wprowadź nazwę dzierżawy dokładnie do aktywowania **Włącz** przycisku.
4. Kliknij pozycję **Włącz** przycisk, aby udzielić uprawnień zbierania danych w usłudze programu Connect Health. Wkrótce zostaną zastosowane zmiany. 
5. Postępuj zgodnie z [proces instalacji](how-to-connect-health-agent-install.md) ponownie zainstalować agenta na serwerach do monitorowania i usługi będą znajdować się w portalu.  


## <a name="next-steps"></a>Kolejne kroki
* [Przegląd zasad Privacy firmy Microsoft w Centrum zaufania](https://www.microsoft.com/trustcenter)
* [Azure AD Connect i ochrony prywatności użytkowników](reference-connect-user-privacy.md)

