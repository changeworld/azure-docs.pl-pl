---
title: 'Samouczek: Akceptuj & odbierania danych — wersja zapoznawcza usługi Azure Data Share'
description: Samouczek — akceptowanie i odbieranie danych przy użyciu wersji zapoznawczej usługi Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 235ef25b2d655c4388dee5bdcf88d179f3373697
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327398"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Samouczek: Akceptowanie i odbieranie danych przy użyciu wersji zapoznawczej usługi Azure Data Share

W tym samouczku dowiesz się, jak zaakceptować zaproszenie do udziału danych przy użyciu usługi Azure Data Share Preview. Dowiesz się, jak odbierać dane udostępniane Tobie, a także jak włączyć okres regularnego odświeżania, aby upewnić się, że zawsze masz najnowszą migawkę danych udostępnionych Tobie. 

> [!div class="checklist"]
> * Jak zaakceptować zaproszenie do korzystania z wersji zapoznawczej udziału danych platformy Azure
> * Utwórz konto w wersji zapoznawczej udziału danych platformy Azure
> * Określ miejsce docelowe dla danych
> * Utwórz subskrypcję udziału danych na potrzeby zaplanowanego odświeżania

## <a name="prerequisites"></a>Wymagania wstępne
Przed zaakceptowaniem zaproszenia do udziału danych musisz udostępnić kilka zasobów platformy Azure, które są wymienione poniżej. 

Przed zaakceptowaniem zaproszenia udziału danych upewnij się, że wszystkie wymagania wstępne zostały ukończone. 

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Storage: Jeśli jeszcze tego nie masz, możesz utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Zaproszenie do udziału danych: Zaproszenie od Microsoft Azure z podmiotem zatytułowanym "zaproszenie do udziału danych platformy Azure z **<yourdataprovider@domain.com>** ".
* Uprawnienie do dodawania przypisywania roli do konta magazynu, które jest obecne w uprawnieniach *Microsoft. Autoryzacja/Przypisywanie ról/zapis* . To uprawnienie istnieje w roli właściciela. 
* Rejestracja dostawcy zasobów dla elementu Microsoft. datashare. Informacje o tym, jak to zrobić, zawiera dokumentacja [dostawcy zasobów platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) . 

> [!IMPORTANT]
> Aby zaakceptować i odebrać udział danych platformy Azure, musisz najpierw zarejestrować dostawcę zasobów Microsoft. datashare i musisz być właścicielem konta magazynu, do którego są akceptowane dane. Postępuj zgodnie z instrukcjami opisanymi w temacie [Rozwiązywanie problemów z usługą Azure Data Share w wersji zapoznawczej](data-share-troubleshoot.md) , aby zarejestrować dostawcę zasobów udziału danych oraz dodać siebie jako właściciela konta magazynu. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otwórz zaproszenie

Sprawdź zaproszenie od dostawcy danych w skrzynce odbiorczej. Zaproszenie pochodzi z Microsoft Azure, zatytułowane **zaproszenie udziału danych platformy Azure z <yourdataprovider@domain.com>** . Zwróć uwagę na nazwę udziału, aby upewnić się, że zaakceptujesz prawidłowy udział, jeśli istnieje wiele zaproszeń. 

Wybierz pozycję **Wyświetl zaproszenie** , aby zobaczyć zaproszenie na platformie Azure. Spowoduje to przejście do widoku otrzymane udziały.

Lista ![zaproszeń](./media/invitations.png "zaproszeń") 

Wybierz udział, który chcesz wyświetlić. 

## <a name="accept-invitation"></a>Zaakceptuj zaproszenie
Upewnij się, że wszystkie pola są przeglądane, w tym **warunki użytkowania**. Jeśli akceptujesz warunki użytkowania, musisz zaznaczyć pole wyboru, aby wskazać, że zgadzasz się. 

![Warunki użytkowania](./media/terms-of-use.png "warunki użytkowania") 

W obszarze *docelowe konto udziału danych*wybierz subskrypcję i grupę zasobów, w ramach której będziesz wdrażać udział danych. 

W polu **konto udostępniania danych** wybierz pozycję **Utwórz nową** , jeśli nie masz istniejącego konta udziału danych. W przeciwnym razie wybierz istniejące konto udziału danych, do którego chcesz zaakceptować udział danych. 

W polu *Nazwa otrzymanego udziału* można pozostawić wartość domyślną określoną przez dane lub określić nową nazwę dla odebranego udziału. 

Docelowe konto udziału danych(./media/target-data-share.png "docelowego") ![konta]udziału danych 

Po uzgodnieniu warunków użytkowania i określeniu lokalizacji dla udziału wybierz opcję *Zaakceptuj i skonfiguruj*. W przypadku wybrania tej opcji zostanie utworzona subskrypcja udziału, a na następnym ekranie zostanie wyświetlony monit o wybranie docelowego konta magazynu, do którego mają zostać skopiowane dane. 

![Akceptuj]opcje(./media/accept-options.png "Zaakceptuj") opcje 

Jeśli wolisz zaakceptować zaproszenie teraz, ale skonfigurować magazyn w późniejszym czasie, wybierz opcję *Zaakceptuj i skonfiguruj później*. Ta opcja umożliwia późniejsze skonfigurowanie docelowego konta magazynu. Aby kontynuować konfigurowanie magazynu później, zobacz [jak skonfigurować swoją stronę konta magazynu](how-to-configure-mapping.md) , aby uzyskać szczegółowe instrukcje dotyczące wznawiania konfiguracji udziału danych. 

Jeśli nie chcesz zaakceptować zaproszenia, wybierz pozycję *Odrzuć*. 

## <a name="configure-storage"></a>Konfigurowanie magazynu
W obszarze *docelowe ustawienia magazynu*wybierz subskrypcję, grupę zasobów i konto magazynu, do których chcesz otrzymywać dane. 

Docelowy(./media/target-storage-settings.png "Magazyn") ![ustawień magazynu] 

Aby otrzymywać regularne odświeżanie danych, upewnij się, że zostały włączone ustawienia migawki. Należy zauważyć, że tylko harmonogram ustawień migawek zostanie wyświetlony, jeśli dostawca danych włączył go w udziale danych. 

Ustawienia migawki ![ustawień](./media/snapshot-settings.png "migawek") 

Wybierz pozycję *Zapisz*. 

## <a name="trigger-a-snapshot"></a>Wyzwalanie migawki

Możesz wyzwolić migawkę na karcie otrzymane udziały — > Szczegóły, wybierając pozycję **Wyzwól migawkę**. Tutaj można wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy otrzymujesz dane od dostawcy danych, wybierz pozycję pełna kopia. 

![Wyzwalanie](./media/trigger-snapshot.png "migawki wyzwalacza") migawek 

Po *pomyślnym*zakończeniu ostatniego uruchomienia programu Otwórz konto magazynu, aby wyświetlić odebrane dane. 

Aby sprawdzić, które konto magazynu zostało użyte, wybierz pozycję **zestawy danych**. 

![](./media/consumer-datasets.png "Mapowanie zestawu danych") konsumenta 

## <a name="view-history"></a>Wyświetl historię
Aby wyświetlić historię migawek, przejdź do obszaru otrzymane udziały — Historia >. W tym miejscu znajdziesz historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 60 dni. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak akceptować i odbierać udział danych platformy Azure. Aby dowiedzieć się więcej na temat pojęć związanych z udostępnianiem danych platformy Azure, przejdź do @no__t 0Concepts: Terminologia dotycząca udziałów danych platformy Azure @ no__t-0.