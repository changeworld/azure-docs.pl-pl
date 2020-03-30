---
title: Sprawdzanie poprawności kodu XML za pomocą schematów
description: Dodawanie schematów w celu sprawdzania poprawności dokumentów XML w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979378"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Waliduj kod XML przy użyciu schematów w usłudze Azure Logic Apps z Pakietem integracyjnym dla przedsiębiorstw

Aby sprawdzić, czy dokumenty używają prawidłowego kodu XML i mają oczekiwane dane w wstępnie zdefiniowanym formacie dla scenariuszy integracji przedsiębiorstwa w usłudze Azure Logic Apps, aplikacja logiki może używać schematów. Schemat można również sprawdzić poprawność komunikatów, które aplikacje logiki wymiany w scenariuszach business-to-business (B2B).

Aby uzyskać limity związane z kontami integracji i artefaktami, takimi jak schematy, zobacz [Limity i informacje o konfiguracji dla usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* [Konto integracji,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) na którym można przechowywać schematy i inne artefakty dla rozwiązań integracji przedsiębiorstwa i business-to-business (B2B). 

  Jeśli schemat jest [2 MB lub mniejszy](#smaller-schema), można dodać schemat do konta integracji bezpośrednio z witryny Azure portal. Jednak jeśli schemat jest większy niż 2 MB, ale nie większy niż [limit rozmiaru schematu,](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)można przekazać schemat do konta magazynu platformy Azure. 
  Aby dodać ten schemat do konta integracji, można następnie połączyć się z kontem magazynu z konta integracji. 
  W tym zadaniu oto potrzebne elementy: 

  * [Konto usługi Azure Storage,](../storage/common/storage-account-overview.md) na którym można utworzyć kontener obiektów blob dla schematu. Dowiedz się, jak [utworzyć konto magazynu](../storage/common/storage-account-create.md). 

  * Kontener obiektów blob do przechowywania schematu. Dowiedz się, jak [utworzyć kontener obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Identyfikator URI zawartości kontenera jest potrzebny później po dodaniu schematu do konta integracji.

  * [Usługa Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), której można używać do zarządzania kontami magazynu i kontenerami obiektów blob. 
  Aby użyć Eksploratora magazynu, wybierz jedną z tych opcji w tym miejscu:
  
    * W witrynie Azure portal znajdź i wybierz swoje konto magazynu. 
    Z menu konta magazynu wybierz pozycję **Eksplorator magazynu**.

    * W przypadku wersji klasycznej [pobierz i zainstaluj Eksploratora usługi Azure Storage](https://www.storageexplorer.com/). 
    Następnie należy połączyć Eksploratora magazynu z kontem magazynu, wykonując czynności opisane w programie [Wprowadzenie do Eksploratora magazynu](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Aby dowiedzieć się więcej, zobacz [Szybki start: Tworzenie obiektu blob w magazynie obiektów za pomocą Eksploratora usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Nie potrzebujesz aplikacji logiki podczas tworzenia i dodawania schematów. Jednak aby użyć schematu, aplikacja logiki musi połączyć się z kontem integracji, gdzie można przechowywać tego schematu. Dowiedz [się, jak połączyć aplikacje logiki z kontami integracji.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) Jeśli nie masz jeszcze aplikacji logiki, dowiedz się, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Dodawanie schematów

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure.

1. Aby znaleźć i otworzyć konto integracji, w głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wpisz "konto integracji". Wybierz **konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Wybierz konto integracji, na którym chcesz dodać schemat, na przykład:

   ![Wybierz konto integracji](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na stronie **Przegląd** konta integracji w obszarze **Składniki**wybierz kafelek **Schematy.**

   ![Wybierz "Schematy"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Po otwarciu strony **Schematy** wybierz pozycję **Dodaj**.

   ![Wybierz "Dodaj"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Na podstawie rozmiaru pliku schematu (xsd) wykonaj kroki przekazywania schematu o [rozmiarze do 2 MB](#smaller-schema) lub [większym niż 2 MB, do 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Dodawanie schematów o przesiewu do 2 MB

1. W obszarze **Dodaj schemat**wprowadź nazwę schematu. 
   Zachowaj mały **plik** zaznaczony. Obok pola **Schemat** wybierz ikonę folderu. Znajdź i wybierz schemat, który przesyłasz, na przykład:

   ![Przekazywanie mniejszego schematu](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Gdy będziesz gotowy, wybierz przycisk **OK**.

   Po zakończeniu przekazywania schematu schemat pojawia się na liście **Schematy.**

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Dodawanie schematów więcej niż 2 MB

Aby dodać większe schematy, można przekazać schemat do kontenera obiektów blob platformy Azure na koncie usługi Azure storage. Kroki dodawania schematów różnią się w zależności od tego, czy kontener obiektu blob ma publiczny dostęp do odczytu. Dlatego najpierw sprawdź, czy kontener obiektów blob ma publiczny dostęp do odczytu, wykonując następujące kroki: [Ustaw poziom dostępu publicznego dla kontenera obiektów blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Sprawdzanie poziomu dostępu do kontenera

1. Otwórz Eksploratora usługi Azure Storage. W oknie Eksploratora rozwiń subskrypcję platformy Azure, jeśli nie została jeszcze rozwinięta.

1. Rozwiń **rozszerz konta magazynu** > {*konto magazynu*} > **kontenery obiektów blob**. Wybierz kontener obiektów blob.

1. Z menu skrótów kontenera obiektów blob wybierz polecenie **Ustaw publiczny poziom dostępu**.

   * Jeśli kontener obiektów blob ma co najmniej publiczny dostęp, wybierz pozycję **Anuluj**i wykonaj następujące kroki później na tej stronie: [Przekaż do kontenerów z dostępem publicznym](#public-access)

     ![Dostęp publiczny](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Jeśli kontener obiektów blob nie ma dostępu publicznego, wybierz pozycję **Anuluj**i wykonaj następujące czynności później na tej stronie: [Przekaż do kontenerów bez dostępu publicznego](#public-access)

     ![Brak publicznego dostępu](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Przekazywanie do kontenerów z dostępem publicznym

1. Przekaż schemat do konta magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przesyłania wybierz przekazany schemat. Na pasku narzędzi wybierz pozycję **Kopiuj adres URL,** aby skopiować adres URL schematu.

1. Wróć do witryny Azure portal, w której jest otwarte okienko **Dodaj schemat.** 
   Wprowadź nazwę zestawu. 
   Wybierz **opcję Duży plik (większy niż 2 MB).** 

   Teraz pojawi się pole **Identyfikator URI zawartości,** a nie pole **Schemat.**

1. W polu **Identyfikator URI zawartości** wklej adres URL schematu. 
   Zakończ dodawanie schematu.

Po zakończeniu przekazywania schematu schemat pojawia się na liście **Schematy.** Na stronie **Przegląd** konta integracji w obszarze **Składniki**kafelek **Schematy** pokazuje teraz liczbę przekazanych schematów.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Przesyłanie do kontenerów bez dostępu publicznego

1. Przekaż schemat do konta magazynu. 
   W oknie po prawej stronie wybierz pozycję **Przekaż**.

1. Po zakończeniu przekazywania wygeneruj sygnaturę dostępu współdzielonego (SAS) dla schematu. 
   Z menu skrótów schematu wybierz polecenie **Pobierz podpis dostępu współdzielonego**.

1. W okienku **Podpis dostępu udostępnionego** wybierz pozycję **Generuj identyfikator URI** > podpisu dostępu współdzielonego na poziomie**kontenera**. 
   Po wygenerowaniu adresu URL sygnatury dostępu Współdzielonego obok pola **adresu URL** wybierz pozycję **Kopiuj**.

1. Wróć do witryny Azure portal, w której jest otwarte okienko **Dodaj schemat.** Wybierz **duży plik**.

   Teraz pojawi się pole **Identyfikator URI zawartości,** a nie pole **Schemat.**

1. W polu **Identyfikator URI zawartości** wklej poprzednio wygenerowany identyfikator URI sygnatury dostępu Współdzielonego. Zakończ dodawanie schematu.

Po zakończeniu przekazywania schematu schemat pojawia się na liście **Schematy.** Na stronie **Przegląd** konta integracji w obszarze **Składniki**kafelek **Schematy** pokazuje teraz liczbę przekazanych schematów.

## <a name="edit-schemas"></a>Edytowanie schematów

Aby zaktualizować istniejący schemat, należy przekazać nowy plik schematu, który zawiera żądane zmiany. Można jednak najpierw pobrać istniejący schemat do edycji.

1. W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>znajdź i otwórz swoje konto integracji, jeśli nie jest jeszcze otwarty.

1. W głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. 
   W polu wyszukiwania wpisz "konto integracji". 
   Wybierz **konta integracji**.

1. Wybierz konto integracji, na którym chcesz zaktualizować schemat.

1. Na stronie **Przegląd** konta integracji w obszarze **Składniki**wybierz kafelek **Schematy.**

1. Po otwarciu strony **Schematy** wybierz schemat. 
   Aby najpierw pobrać i edytować schemat, wybierz pozycję **Pobierz**i zapisz schemat.

1. Gdy będziesz gotowy do przekazania zaktualizowanego schematu, na stronie **Schematy** wybierz schemat, który chcesz zaktualizować, i wybierz pozycję **Aktualizuj**.

1. Znajdź i wybierz zaktualizowany schemat, który chcesz przekazać. 
   Po zakończeniu przekazywania pliku schematu zaktualizowany schemat pojawi się na liście **Schematy.**

## <a name="delete-schemas"></a>Usuwanie schematów

1. W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>znajdź i otwórz swoje konto integracji, jeśli nie jest jeszcze otwarty.

1. W głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. 
   W polu wyszukiwania wpisz "konto integracji". 
   Wybierz **konta integracji**.

1. Wybierz konto integracji, na którym chcesz usunąć schemat.

1. Na stronie **Przegląd** konta integracji w obszarze **Składniki**wybierz kafelek **Schematy.**

1. Po otwarciu strony **Schematy** wybierz schemat i wybierz pozycję **Usuń**.

1. Aby potwierdzić, że chcesz usunąć schemat, wybierz opcję **Tak**.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
* [Dowiedz się więcej o mapach](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Dowiedz się więcej o transformacjach](../logic-apps/logic-apps-enterprise-integration-transform.md)
