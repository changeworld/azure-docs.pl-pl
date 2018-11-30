---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: ef6d5d22f70d5fff38f90b457a7c945ab59fc67c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331308"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

W przeciwieństwie do kolejek usługi Service Bus, w którym każdy komunikat jest przetwarzany przez jednego konsumenta, tematy i subskrypcje zapewniają formę komunikacji przy użyciu wzorca publikowania/subskrybowania "jeden do wielu". Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia.

Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtrów dla tematu w każdej subskrypcji. Reguły filtrowania umożliwiają filtrowanie, lub ograniczyć zakres komunikatów w temacie odbieranych przez określone subskrypcje tematów.

Subskrypcji i tematów usługi Service Bus umożliwiają skalowanie i przetwarzanie dużej liczby komunikatów przez wielu użytkowników i aplikacji.

## <a name="create-a-namespace"></a>Tworzenie przestrzeni nazw
Aby rozpocząć korzystanie z tematów i subskrypcji usługi Service Bus na platformie Azure, należy najpierw utworzyć *przestrzeń nazw usługi*. Przestrzeń nazw zapewnia kontener określania zakresu na potrzeby adresowania zasobów usługi Service Bus w aplikacji.

Aby utworzyć przestrzeń nazw:

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W okienku nawigacji po lewej stronie portalu kliknij **Utwórz zasób**, następnie kliknij przycisk **integracji dla przedsiębiorstw**, a następnie kliknij przycisk **usługi Service Bus**.
3. W oknie dialogowym **Tworzenie przestrzeni nazw** wprowadź nazwę przestrzeni nazw. System od razu sprawdza, czy nazwa jest dostępna.
4. Po upewnieniu się, że nazwa przestrzeni nazw jest dostępna, wybierz warstwę cenową (Podstawowa, Standardowa lub Premium).
5. W polu **Subskrypcja** wybierz subskrypcję platformy Azure, w której ma zostać utworzona przestrzeń nazw.
6. W **grupy zasobów** pola, wybierz istniejącą grupę zasobów, w którym są przechowywane w przestrzeni nazw lub Utwórz nową.      
7. W polu **Lokalizacja** wybierz kraj lub region, w którym powinna być hostowana przestrzeń nazw.
   
    ![Create namespace][create-namespace]
8. Kliknij przycisk **Utwórz**. W systemie zostanie utworzona i włączona przestrzeń nazw. Proces aprowizacji zasobów dla konta w systemie może potrwać kilka minut.

### <a name="obtain-the-credentials"></a>Uzyskiwanie poświadczeń
1. Na liście przestrzeni nazw kliknij nowo utworzoną nazwę przestrzeni nazw.
2. W **przestrzeni nazw usługi Service Bus** okienku kliknij **zasady dostępu współdzielonego**.
3. W **zasady dostępu współdzielonego** okienku kliknij **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. W **zasady: RootManageSharedAccessKey** okienku, kliknij przycisk kopiowania obok **parametry połączenia — klucz podstawowy**, aby skopiować parametry połączenia do Schowka w celu późniejszego użycia.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


