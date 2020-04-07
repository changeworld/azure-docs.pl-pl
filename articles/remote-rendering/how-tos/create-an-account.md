---
title: Tworzenie konta zdalnego renderowania platformy Azure
description: W tym artykule opisano kroki tworzenia konta dla zdalnego renderowania platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681300"
---
# <a name="create-an-azure-remote-rendering-account"></a>Tworzenie konta zdalnego renderowania platformy Azure

W tym rozdziale znajdziesz instrukcje tworzenia konta dla usługi **Azure Remote Rendering.** Prawidłowe konto jest obowiązkowe do ukończenia któregokolwiek z szybkich startów lub samouczków.

## <a name="create-an-account"></a>Tworzenie konta

Aby utworzyć konto usługi zdalnego renderowania platformy Azure, potrzebne są następujące kroki:

1. Przejdź do [strony Podgląd rzeczywistości mieszanej](https://aka.ms/MixedRealityPrivatePreview)
1. Kliknij przycisk "Utwórz zasób"
1. W polu wyszukiwania ("Wyszukaj w rynku" wpisz "Zdalne renderowanie" i naciśnij "enter".
1. Na liście wyników kliknij kafelek "Zdalne renderowanie"
1. Na następnym ekranie kliknij przycisk "Utwórz". Zostanie otwarty formularz, aby utworzyć nowe konto zdalnego renderowania:
    1. Ustaw "Nazwę zasobu" na nazwę konta
    1. W razie potrzeby zaktualizuj "Subskrypcję"
    1. Ustaw "Grupę zasobów" na wybraną grupę zasobów
1. Po utworzeniu konta przejdź do niego i:
    1. Na karcie *Przegląd* zanotuj "Identyfikator konta"
    1. Na karcie *Ustawienia > klucze dostępu* zanotuj "Klucz podstawowy" - jest to klucz tajnego konta konta

### <a name="retrieve-the-account-information"></a>Pobieranie informacji o koncie

Przykłady i samouczki wymagają podania identyfikatora konta i klucza. Na przykład w pliku **arrconfig.json,** który jest używany dla przykładowych skryptów programu PowerShell:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Zobacz [listę dostępnych regionów,](../reference/regions.md) aby wypełnić opcję *regionu.*

Wartości dla **`arrAccountId`** **`arrAccountKey`** i można je znaleźć w portalu, jak opisano w następujących krokach:

* Przejdź do witryny [Azure Portal](https://www.portal.azure.com).
* Znajdź swoje **"Konto zdalnego renderowania"** - powinno ono znajdować się na liście **"Ostatnie zasoby".** Można go również wyszukać na pasku wyszukiwania u góry. W takim przypadku upewnij się, że subskrypcja, której chcesz użyć, jest zaznaczona w filtrze Domyślna subskrypcja (ikona filtru obok paska wyszukiwania):

![Filtr subskrypcji](./media/azure-subscription-filter.png)

Kliknięcie na twoje konto powoduje wyświetlenie tego ekranu, na którym od razu pojawi **się identyfikator konta:**

![Identyfikator konta platformy Azure](./media/azure-account-id.png)

W przypadku klawisza wybierz **klawisze dostępu** w panelu po lewej stronie. Na następnej stronie znajduje się klucz podstawowy i pomocniczy:

![Klucze dostępu platformy Azure](./media/azure-account-primary-key.png)

Wartość dla **`arrAccountKey`** może być kluczem podstawowym lub pomocniczym.

## <a name="link-storage-accounts"></a>Łączenie kont magazynu

W tym akapicie wyjaśniono, jak połączyć konta magazynu z kontem zdalnego renderowania. Gdy konto magazynu jest połączony nie jest konieczne do generowania identyfikatora URI sygnatury dostępu Współdzielonego za każdym razem, gdy chcesz wchodzić w interakcje z danymi na koncie, na przykład podczas ładowania modelu. Zamiast tego można użyć nazw kont magazynu bezpośrednio, jak opisano w [sekcji ładowania modelu](../concepts/models.md#loading-models).

Kroki opisane w tym akapicie muszą być wykonywane dla każdego konta magazynu, które powinno używać tej alternatywnej metody dostępu. Jeśli konta magazynu nie zostały jeszcze utworzone, można przejść przez odpowiedni krok w [konwersji modelu renderowania szybkiego startu](../quickstarts/convert-model.md#storage-account-creation).

Teraz zakłada się, że masz konto magazynu. Przejdź do konta magazynu w portalu i przejdź do karty **Kontrola dostępu (IAM)** dla tego konta magazynu:

![IAM konta magazynu](./media/azure-storage-account.png)

 Upewnij się, że masz uprawnienia właściciela do tego konta magazynu, aby upewnić się, że można dodawać przypisania ról. Jeśli nie masz dostępu, opcja **Dodaj przypisanie roli** zostanie wyłączona.

 Należy dodać trzy różne role, zgodnie z opisem w następnych krokach. Jeśli nie podasz wszystkich trzech poziomów dostępu, będą problemy z uprawnieniami podczas próby uzyskania dostępu do konta magazynu.

 Kliknij przycisk **Dodaj** na kafelku "Dodaj przypisanie roli", aby dodać pierwszą rolę:

![IAM konta magazynu](./media/azure-add-role-assignment.png)

* Pierwszą rolą do przypisania jest **Owner,** jak pokazano na powyższym zrzucie ekranu. 
* Wybierz **pozycję Konto zdalnego renderowania** z listy rozwijanej ***Przypisz dostęp do** listy rozwijanej.
* Wybierz konto subskrypcji i renderowania zdalnego w ostatnich rozwijanych.

Powtórz dodawanie nowych ról jeszcze dwa razy dla odpowiednich wyborów z listy rozwijanej **Rola:**
* **Współautor konta magazynu**
* **Współautor danych obiektów blob magazynu**

Pozostałe listy rozwijane są wybierane jako w pierwszym kroku.

Jeśli dodano wszystkie trzy role, twoje konto zdalnego renderowania platformy Azure ma dostęp do konta magazynu przy użyciu tożsamości usługi zarządzanej przypisanych do systemu.

## <a name="next-steps"></a>Następne kroki

* [Authentication](authentication.md)
* [Używanie interfejsów API zaplecza azure do uwierzytelniania](frontend-apis.md)
* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
