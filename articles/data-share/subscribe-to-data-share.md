---
title: Samouczek — Zaakceptuj i odbierać dane przy użyciu udziału danych platformy Azure w wersji zapoznawczej
description: Samouczek — Zaakceptuj i odbierać dane przy użyciu udziału danych platformy Azure w wersji zapoznawczej
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 2dc4994d88fc03c23a6d5722d6018c926e7d6b8c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788164"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Samouczek: Zaakceptuj i odbierać dane przy użyciu udziału danych platformy Azure w wersji zapoznawczej

W tym samouczku dowiesz się, jak zaakceptować zaproszenie udostępniania danych, za pomocą udostępniania danych platformy Azure w wersji zapoznawczej. Dowiesz się, jak odbierać dane są udostępniane Tobie, jak również sposób włączania interwał odświeżania regularne, aby zawsze mieć najnowsze migawkę danych udostępniony Tobie. 

> [!div class="checklist"]
> * Sposób zaakceptować zaproszenie udostępniania danych Azure w wersji zapoznawczej
> * Tworzenie konta usługi Azure udostępniania danych w wersji zapoznawczej
> * Określ miejsce docelowe danych
> * Utwórz subskrypcję do udziału danych do zaplanowanego odświeżania

## <a name="prerequisites"></a>Wymagania wstępne
Zanim można zaakceptować zaproszenie udostępniania danych, należy zarezerwować szereg zasobów platformy Azure, które są wymienione poniżej. 

Upewnij się, czy wszystkie wymagania wstępne zostały spełnione przed zaakceptowaniem zaproszenie udostępniania danych. 

* Subskrypcja platformy Azure: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Storage: Jeśli nie masz jeszcze jeden, możesz utworzyć [konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Zaproszenie do udziału danych: Zaproszenie z Microsoft Azure z tematem pod tytułem "zaproszenie udostępniania danych platformy Azure od **<yourdataprovider@domain.com>** ".

> [!IMPORTANT]
> Aby zaakceptować i odbierać udziału danych platformy Azure, najpierw należy zarejestrować dostawcę zasobów Microsoft.DataShare i musisz być właścicielem konta magazynu, które akceptują dane. Postępuj zgodnie z instrukcjami opisanymi w [Rozwiązywanie problemów z dane udostępniania wersji zapoznawczej Azure](data-share-troubleshoot.md) Zarejestruj dostawcę zasobów udziału danych, a także dodać siebie jako właściciela konta magazynu. 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Otwórz zaproszenia

Sprawdź skrzynkę odbiorczą w poszukiwaniu zaproszenie z dostawcą danych. Zaproszenie jest z Microsoft Azure, pod tytułem **zaproszenie udostępniania danych platformy Azure od <yourdataprovider@domain.com>** . Zwróć uwagę na nazwy udziału, aby upewnić się, że akceptujesz poprawnego udziału, w przypadku wielu zaproszeń. 

Wybierz **Wyświetl zaproszenia** się zaproszenie na platformie Azure. Spowoduje to przejście do widoku Odebrano udziałów.

![Zaproszenia](./media/invitations.png "lista zaproszeń") 

Wybierz udział, który chcesz wyświetlić. 

## <a name="accept-invitation"></a>Zaakceptuj zaproszenie
Upewnij się, wszystkie pola są przeglądane w tym **warunki użytkowania**. Jeśli wyrazisz zgodę na warunki użytkowania, będzie konieczne pole wyboru, aby wskazać, że wyrażasz zgodę. 

![Warunki użytkowania](./media/terms-of-use.png "warunki użytkowania") 

W obszarze *konta udziału danych docelowego*, wybierz subskrypcję i grupę zasobów, której będzie wdrożenie do udziału danych. 

Dla **konto udziału danych** pól, zaznacz **Utwórz nową** Jeśli masz istniejące konto udziału danych. W przeciwnym razie wybierz istniejące konto udostępniać dane, chcesz zaakceptować udziału danych do. 

Dla *Odebrano nazwa udziału* pola może pozostaw wartość domyślną, określony przez zapewniają danych lub określ nową nazwę udziału odebrane. 

![Docelowe konto udziału danych](./media/target-data-share.png "danych docelowych udostępnić konto") 

Po zgodę na warunki użytkowania i określono lokalizację udziału, wybierz na *Akceptuj i konfigurowanie*. W przypadku wybrania tej opcji, subskrypcja udział zostanie utworzona i następnego ekranu wyświetli monit o wybierz docelowe konto magazynu, który ma zostać skopiowana do danych. 

![Zaakceptuj opcje](./media/accept-options.png "Zaakceptuj opcje") 

Jeśli chcesz zaakceptować zaproszenie teraz jednak skonfigurować Magazyn w późniejszym czasie, wybierz *zaakceptować i później skonfigurować*. Ta opcja umożliwia później skonfigurować swoje konto magazynu docelowego. Aby kontynuować, później konfiguracji magazynu, zobacz [sposób konfigurowania konta usługi storage](how-to-configure-mapping.md) strona szczegółowe instrukcje dotyczące sposobu wznowić dane mają konfigurację. 

Jeśli nie chcesz zaakceptować zaproszenie, wybierz *Odrzuć*. 

## <a name="configure-storage"></a>Konfigurowanie magazynu
W obszarze *ustawienia magazynu docelowego*, wybierz subskrypcję, grupy zasobów i konto magazynu, które chcesz odbierać dane do. 

![Docelowe ustawienia magazynu](./media/target-storage-settings.png "docelowy magazyn") 

Aby otrzymywać regularne odświeżania danych, upewnij się, że w przypadku włączenia ustawień migawki. Należy pamiętać, że widoczne są tylko ustawienia harmonogramu migawek Jeśli Twój dostawca danych ma on uwzględniony w udziału danych. 

![Ustawienia migawki](./media/snapshot-settings.png "ustawienia migawki") 

Wybierz pozycję *Zapisz*. 

## <a name="trigger-a-snapshot"></a>Wyzwalacz migawki

Możesz wyzwolić migawki w udziałach Odebrano -> Karta Szczegóły, wybierając **migawki wyzwalacza**. W tym miejscu możesz wyzwolić pełną lub przyrostową migawkę danych. Jeśli po raz pierwszy odbierania danych z dostawcą danych, wybierz pełnej kopii. 

![Wyzwalacz migawki](./media/trigger-snapshot.png "migawki wyzwalacza") 

Gdy jest ostatni stan uruchomienia *pomyślne*, otwórz konto magazynu, aby wyświetlić odebranych danych. 

Aby sprawdzić, które konto magazynu, możesz użyć, wybierz **zestawów danych**. 

![Zestawy danych klientów](./media/consumer-datasets.png "mapowanie zestawu danych klientów") 

## <a name="view-history"></a>Wyświetlanie historii
Aby wyświetlić historię z migawki, przejdź do udziałów Odebrano -> Historia. W tym miejscu można znaleźć historię wszystkich migawek, które zostały wygenerowane w ciągu ostatnich 60 dni. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku możesz dzięki modelom uczenia jak akceptować i odbierać udziału danych platformy Azure. Aby dowiedzieć się więcej na temat pojęć udziału danych platformy Azure, w dalszym ciągu [pojęcia: Usługi Azure Data udostępnianie terminologii](terminology.md).