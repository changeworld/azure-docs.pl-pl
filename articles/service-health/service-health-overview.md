---
title: Przegląd kondycji usługi | Dokumenty firmy Microsoft
description: Spersonalizowane informacje o tym, jak twoje aplikacje platformy Azure są dotknięte bieżącymi i przyszłymi problemami z usługami platformy Azure i konserwacją.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898439"
---
# <a name="service-health-overview"></a>Omówienie kondycji usługi

Usługa Service Health zapewnia konfigurowalny pulpit nawigacyjny, który śledzi kondycję usług platformy Azure w regionach, w których ich używasz. Na tym pulpicie nawigacyjnym można śledzić aktywne zdarzenia, takie jak bieżące problemy z usługami, planowana konserwacja lub odpowiednie porady dotyczące kondycji. Gdy zdarzenia stają się nieaktywne, zostają umieszczone w historii zdrowia przez okres do 90 dni. Na koniec można użyć pulpitu nawigacyjnego kondycji usługi do tworzenia alertów kondycji usługi i zarządzania nimi, które proaktywnie powiadamiają cię, gdy problemy z usługą mają wpływ na Ciebie.

## <a name="service-health-events"></a>Zdarzenia dotyczące kondycji usługi

Usługa Service Health śledzi cztery typy zdarzeń kondycji, które mogą mieć wpływ na zasoby:

1. **Problemy z usługą** — problemy z usługami platformy Azure, które wpływają na Ciebie w tej chwili. 
2. **Planowana konserwacja** — nadchodzące konserwacje, które mogą mieć wpływ na dostępność usług w przyszłości.  
3. **Porady dotyczące kondycji** — zmiany w usługach platformy Azure, które wymagają twojej uwagi. Przykłady obejmują, gdy funkcje platformy Azure są przestarzałe lub jeśli przekraczasz przydział użycia.
4. **Klasyfikatorzy zabezpieczeń (wersja zapoznawcza)** — powiadomienia związane z zabezpieczeniami, które mogą mieć wpływ na dostępność usług platformy Azure.

> [!NOTE]
> Aby wyświetlić zdarzenia kondycji usługi, użytkownicy muszą [otrzymać rolę czytelnika](../role-based-access-control/role-assignments-portal.md) w ramach subskrypcji.

## <a name="get-started-with-service-health"></a>Wprowadzenie do usługi Service Health

Aby uruchomić pulpit nawigacyjny kondycji usługi, wybierz kafelek Kondycja usługi na pulpicie nawigacyjnym portalu. Jeśli kafelek został wcześniej usunięty lub używasz niestandardowego pulpitu nawigacyjnego, wyszukaj usługę kondycji usługi w polu "Więcej usług" (lewy dół na pulpicie nawigacyjnym).

![Wprowadzenie do usługi Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Zobacz bieżące problemy, które mają wpływ na Twoje usługi

Widok **problemów z usługą** pokazuje wszelkie bieżące problemy w usługach platformy Azure, które mają wpływ na zasoby. Można zrozumieć, kiedy problem się rozpoczął i jakie usługi i regiony są dotknięte. Możesz również przeczytać najnowszą aktualizację, aby dowiedzieć się, co robi platforma Azure, aby rozwiązać ten problem. 

![Zarządzanie problemem z usługą](./media/service-health-overview/azure-service-health-overview-2.png)

Wybierz kartę **Potencjalny wpływ,** aby wyświetlić określoną listę posiadanych zasobów, na które może mieć wpływ problem. Możesz pobrać listę csv tych zasobów, aby udostępnić je zespołowi.

![Zarządzanie problemem z usługą — wpływ](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Uzyskaj linki i wyjaśnienia do pobrania 

Możesz uzyskać link do problemu do użycia w systemie zarządzania problemami. Możesz pobrać pliki PDF, a czasami pliki CSV, aby udostępnić je osobom, które nie mają dostępu do witryny Azure portal.   

![Zarządzanie problemem z usługą — zarządzanie problemami](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Uzyskaj pomoc techniczną od firmy Microsoft

Skontaktuj się z pomocą techniczną, jeśli zasób pozostaje w złym stanie, nawet po rozwiązaniu problemu.  Użyj linków pomocy technicznej po prawej stronie.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Przypinanie spersonalizowanej mapy zdrowia do pulpitu nawigacyjnego

Filtruj kondycję usługi, aby wyświetlić subskrypcje, regiony i typy zasobów o znaczeniu krytycznym dla firmy. Zapisz filtr i przypnij spersonalizowaną mapę kondycji na świecie do pulpitu nawigacyjnego portalu. 

![Filtrowanie spersonalizowanej mapy zdrowia](./media/service-health-overview/azure-service-health-overview-6a.png)

![Przypinanie spersonalizowanej mapy zdrowia](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurowanie alertów kondycji usługi

Usługa Service Health integruje się z usługą Azure Monitor, aby ostrzegać za pośrednictwem wiadomości e-mail, wiadomości tekstowych i powiadomień w sieci Web, gdy mają wpływ na zasoby o znaczeniu krytycznym dla firmy. Konfigurowanie alertu dziennika aktywności dla odpowiedniego zdarzenia kondycji usługi. Przekieruj ten alert do odpowiednich osób w organizacji za pomocą grup akcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie alertów dla kondycji usługi](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Następne kroki

Skonfiguruj alerty, aby otrzymywać powiadomienia o problemach zdrowotnych. Aby uzyskać więcej informacji, zobacz [Najważniejsze wskazówki dotyczące konfigurowania alertów kondycji usługi Azure](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
