---
title: Walidacja danych XML przy użyciu schematów — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodaj schematy do sprawdzania poprawności XML dokumenty w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 3cca995b353b88cc481cbda68df4211a724f7f09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846373"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Walidacja danych XML przy użyciu schematów w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Aby sprawdzić, czy dokumenty Użyj prawidłowy kod XML i masz oczekiwanych danych w formacie wstępnie zdefiniowanych na potrzeby scenariuszy integracji przedsiębiorstwa w usłudze Azure Logic Apps, aplikacja logiki można użyć schematów. Schemat można zweryfikować wiadomości, które aplikacje logiki programu exchange w różnych scenariuszy biznesowych (B2B).

Limity dotyczące kont integracji i artefakty, takie jak schematy, zobacz [limity i informacje o konfiguracji dla usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) przechowywania swoje schematy i innych artefaktów dla integracji dla przedsiębiorstw i rozwiązań biznesowych (B2B). 

  Jeśli jest schematu [2 MB lub mniej](#smaller-schema), można dodać schematu na koncie integracji bezpośrednio w witrynie Azure portal. Jednak jeśli schematu jest większy niż 2 MB, ale nie większa niż [limit rozmiaru schematu](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), możesz przekazać schematu do konta usługi Azure storage. 
  Aby dodać ten schemat na koncie integracji, konieczne jest połączenie z kontem usługi storage z poziomu konta integracji. 
  Dla tego zadania Oto elementy, które są potrzebne: 

  * [Konto usługi Azure storage](../storage/common/storage-account-overview.md) gdzie Utwórz kontener obiektów blob dla schematu. Dowiedz się, jak [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md). 

  * Kontener obiektów blob do przechowywania schematu. Dowiedz się, jak [Utwórz kontener obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Będą potrzebne identyfikator URI zawartości z kontenerem później podczas dodawania schemat na koncie integracji.

  * [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md), których można użyć do zarządzania kontami magazynu i kontenerach obiektów blob. 
  Aby użyć Eksploratora usługi Storage, wybierz jedną z opcji w tym miejscu:
  
    * W witrynie Azure portal Znajdź i wybierz swoje konto magazynu. 
    Wybierz z menu konta magazynu, **Eksploratora usługi Storage**.

    * W przypadku klasycznej wersji [pobieranie i instalowanie Eksploratora usługi Azure Storage](https://www.storageexplorer.com/). 
    Następnie połącz Eksplorator usługi Storage do konta magazynu, wykonując kroki opisane w [wprowadzenie do Eksploratora usługi Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Aby dowiedzieć się więcej, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów przy użyciu usługi Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Aplikacja logiki, gdy tworzenie i dodawanie schematów nie jest konieczne. Jednak aby używać schematu, potrzebuje aplikacja logiki łączenia z kontem integracji, gdzie przechowujesz tego schematu. Dowiedz się, [sposobu łączenia aplikacji logiki z konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Dowiedz się, jeśli nie masz jeszcze aplikacji logiki, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Dodawanie schematów

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

1. Aby znaleźć i otworzyć konto integracji w głównym menu platformy Azure, wybierz pozycję **wszystkich usług**. W polu wyszukiwania wprowadź "konto integracji". Wybierz **kont integracji**.

   ![Znajdź konto integracji](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Wybierz konto integracji, w której chcesz dodać schematu, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na koncie integracji **Przegląd** w obszarze **składniki**, wybierz opcję **schematów** kafelka.

   ![Wybierz pozycję "Schematy"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Po **schematów** zostanie otwarta strona, wybierz polecenie **Dodaj**.

   ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Na podstawie pliku schematu (XSD) rozmiaru, postępuj zgodnie z instrukcjami przekazywania schematu, który jest albo [do 2 MB](#smaller-schema) lub [więcej niż 2 MB, maksymalnie 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Dodaj schematy do 2 MB

1. W obszarze **schemat Dodaj**, wprowadź nazwę dla schematu. 
   Zachowaj **mały plik** wybrane. Obok pozycji **schematu** , wybierz ikonę folderu. Znajdź i wybierz schemat, który jest przekazywany, na przykład:

   ![Przekaż mniejszy schematu](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   Po zakończeniu przekazywania schematu schemat pojawi się w **schematów** listy.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Dodawanie schematów, więcej niż 2 MB

Aby dodać większej schematów, możesz przekazać schematu do kontenera obiektów blob platformy Azure w ramach konta magazynu platformy Azure. Twoje kroki, aby dodać schematów się różnić w zależności czy kontenera obiektów blob jest publiczny dostęp do odczytu. Dlatego najpierw należy sprawdzić, czy kontenera obiektów blob ma publiczny dostęp do odczytu, wykonaj następujące czynności: [Ustaw poziom dostępu publicznego do kontenera obiektów blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Sprawdź poziom dostępu do kontenera

1. Otwórz Eksplorator usługi Azure Storage. W oknie Eksploratora rozwiń subskrypcji platformy Azure, jeśli nie jest jeszcze rozwinięty.

1. Rozwiń **kont magazynu** > {*swoje konto magazynu*} > **kontenerach obiektów Blob**. Wybierz kontener obiektów blob.

1. Wybierz z menu skrótów dla kontenera obiektów blob, **Ustaw poziom dostępu publicznego**.

   * Jeśli co najmniej publicznego dostępu do kontenera obiektów blob, wybierz **anulować**i wykonaj następujące czynności w późniejszym czasie na tę stronę: [Przekaż do kontenerów przy użyciu dostępu publicznego](#public-access)

     ![Dostęp publiczny](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Jeśli kontener obiektów blob nie ma publicznego dostępu, wybierz opcję **anulować**i wykonaj następujące czynności w późniejszym czasie na tę stronę: [Przekazywanie do kontenerów, bez dostępu publicznego](#public-access)

     ![Brak dostępu publicznego](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Przekaż do kontenerów przy użyciu dostępu publicznego

1. Przekaż schemat do konta magazynu. 
   W oknie po prawej stronie wybierz **przekazywanie**.

1. Po zakończeniu przekazywania wybierz przekazany schematu. Na pasku narzędzi wybierz **URL kopii** tak, aby skopiować adres URL schematu.

1. Wróć do witryny Azure portal gdzie **schemat Dodaj** okienko jest otwarte. 
   Wprowadź nazwę zestawu. 
   Wybierz **duży plik (więcej niż 2 MB)** . 

   **Identyfikator URI zawartości** pojawi się pole, a nie **schematu** pole.

1. W **identyfikator URI zawartości** pole, wklej adres URL usługi schematu. 
   Zakończ, dodawanie schematu.

Po zakończeniu przekazywania schematu schemat pojawi się w **schematów** listy. Na koncie integracji **Przegląd** w obszarze **składniki**, **schematów** Kafelek pokazuje teraz liczba przekazanych schematów.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Przekazywanie do kontenerów, bez dostępu publicznego

1. Przekaż schemat do konta magazynu. 
   W oknie po prawej stronie wybierz **przekazywanie**.

1. Po zakończeniu przekazywania Generowanie sygnatury dostępu współdzielonego (SAS) dla schematu. 
   Wybierz z menu skrótów dla swojej schematu, **Uzyskaj sygnaturę dostępu współdzielonego**.

1. W **udostępnionej sygnatury dostępu** okienku wybierz **identyfikatora URI sygnatury dostępu współdzielonego na poziomie kontenera Generuj** > **Utwórz**. 
   Po adres URL sygnatury dostępu Współdzielonego pobiera wygenerowany, obok **adresu URL** wybierz **kopiowania**.

1. Wróć do witryny Azure portal gdzie **schemat Dodaj** okienko jest otwarte. Wybierz **plików o dużym rozmiarze**.

   **Identyfikator URI zawartości** pojawi się pole, a nie **schematu** pole.

1. W **identyfikator URI zawartości** pole, wklej identyfikator URI sygnatury dostępu Współdzielonego, wcześniej wygenerowany. Zakończ, dodawanie schematu.

Po zakończeniu przekazywania schematu schemat pojawi się w **schematów** listy. Na koncie integracji **Przegląd** w obszarze **składniki**, **schematów** Kafelek pokazuje teraz liczba przekazanych schematów.

## <a name="edit-schemas"></a>Edytowanie schematów

Aby zaktualizować istniejący schemat, musisz Przekaż nowy plik schematu, który zawiera zmiany, które mają. Jednakże można najpierw pobrać istniejącego schematu do edycji.

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, Znajdź i Otwórz swoje konto integracji, jeśli nie już otwarty.

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. 
   W polu wyszukiwania wprowadź "konto integracji". 
   Wybierz **kont integracji**.

1. Wybierz konto integracji, w której chcesz aktualizować schematu.

1. Na koncie integracji **Przegląd** w obszarze **składniki**, wybierz opcję **schematów** kafelka.

1. Po **schematów** zostanie otwarta strona, wybierz opcję schematu. 
   Aby pobrać i edytowania schematu pierwszy, wybierz opcję **Pobierz**i Zapisz schematu.

1. Kiedy wszystko będzie gotowe przekazać zaktualizowany schemat **schematów** zaznacz opcję schematu, w którym chcesz zaktualizować, a następnie wybierz pozycję **aktualizacji**.

1. Znajdź i wybierz zaktualizowany schemat, który chcesz przekazać. 
   Po zakończeniu przekazywania pliku schematu zaktualizowany schemat pojawi się w **schematów** listy.

## <a name="delete-schemas"></a>Usuwanie schematów

1. W <a href="https://portal.azure.com" target="_blank">witryny Azure portal</a>, Znajdź i Otwórz swoje konto integracji, jeśli nie już otwarty.

1. W głównym menu platformy Azure, wybierz **wszystkich usług**. 
   W polu wyszukiwania wprowadź "konto integracji". 
   Wybierz **kont integracji**.

1. Wybierz konto integracji, które chcesz usunąć schematu.

1. Na koncie integracji **Przegląd** w obszarze **składniki**, wybierz opcję **schematów** kafelka.

1. Po **schematów** zostanie otwarta strona, wybierz opcję schematu, a następnie wybierz **Usuń**.

1. Aby upewnić się, że chcesz usunąć schematu, wybierz opcję **tak**.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
* [Dowiedz się więcej na temat map](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Dowiedz się więcej na temat przekształceń](../logic-apps/logic-apps-enterprise-integration-transform.md)
