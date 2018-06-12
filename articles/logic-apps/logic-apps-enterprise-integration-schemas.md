---
title: Schematy dla sprawdzanie poprawności kodu XML - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdzanie poprawności dokumentów XML za pomocą schematów dla usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: e23500ec3c16e66b8dc74fcba29e9b58f0b41790
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299066"
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Walidacja danych XML o schematach dla usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw

Schematy potwierdzić, że dokumenty XML, które otrzymujesz są prawidłowe i mają oczekiwane dane w formacie wstępnie zdefiniowane. Schematy również pomóc sprawdzania poprawności komunikatów, które są wymieniane w scenariuszu B2B.

## <a name="add-a-schema"></a>Dodawanie schematu

1. W portalu Azure wybierz **wszystkie usługi**.

    ![Portalu Azure, "Wszystkie usługi"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. W polu filtru wyszukiwania wprowadź **integracji**i wybierz **konta integracji** z listy wyników.

    ![Pole wyszukiwania filtru](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Wybierz **konta integracji** której chcesz dodać schematu.

    ![Lista kont integracji](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Wybierz **schematy** kafelka.

    ![Przykład integracji konta, "Schematy"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Dodaj plik schematu mniejszy niż 2 MB

1. W **schematy** bloku, który zostanie otwarty (z powyższych kroków), wybierz **Dodaj**.

    ![Schematy bloku "Dodaj"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Wprowadź nazwę schematu. Przekaż plik schematu, wybierając ikonę folderu obok **schematu** pole. Po zakończeniu procesu przekazywania, wybierz **OK**.

    ![Zrzut ekranu przedstawiający "Dodaj Schema", z wyróżnioną pozycją "mały plik"](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Dodaj plik schematu większych niż 2 MB (maksymalnie 8 MB)

Te kroki się różnić w zależności na poziom dostępu do kontenera obiektów blob: **publicznego** lub **dostępu anonimowego**.

**Aby ustalić ten poziom dostępu**

1.  Otwórz **Eksploratora usługi Azure Storage**. 

2.  W obszarze **kontenerów obiektów Blob**, wybierz kontener obiektów blob mają. 

3.  Wybierz **zabezpieczeń**, **poziom dostępu**.

Jeśli poziom dostępu do obiektów blob zabezpieczeń jest **publicznych**, wykonaj następujące kroki.

![Azure Eksploratora usługi Storage, "Kontenerów obiektów Blob", "Zabezpieczenia" i "Public" wyróżnione](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Przekaż schematu do konta magazynu, a następnie skopiuj identyfikator URI.

    ![Konto magazynu o identyfikatorze URI wyróżnione](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. W **Dodaj schemat**, wybierz pozycję **plików o dużym**i podaj identyfikator URI w **URI zawartości** pola tekstowego.

    ![Schematy przycisk "Dodaj" i "Dużych plików" wyróżnione](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Jeśli poziom dostępu do obiektów blob zabezpieczeń jest **dostępu anonimowego**, wykonaj następujące kroki.

![Azure Eksploratora usługi Storage, "Kontenerów obiektów Blob", "Zabezpieczenia" i "Brak dostępu anonimowego" wyróżnione](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Przekaż schematu do konta magazynu.

    ![Konto magazynu](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Generowanie sygnatury dostępu współdzielonego dla schematu.

    ![Konto magazynu z wyróżnionym kartę sygnatur dostępu współdzielonego](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. W **Dodaj schemat**, wybierz pozycję **plików o dużym**i podaj identyfikator URI sygnatury dostępu współdzielonego w **URI zawartości** pola tekstowego.

    ![Schematy przycisk "Dodaj" i "Dużych plików" wyróżnione](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. W **schematy** blok konta integracji, nowo dodanego schemat powinien zostać wyświetlony.

    ![Konta integracji z "Schematy" i nowego schematu wyróżnione](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Edytuj schematów

1. Wybierz **schematy** kafelka.

2. Po **schematy** zostanie otwarty blok, wybierz schemat, który chcesz edytować.

3. Na **schematy** bloku, wybierz **Edytuj**.

    ![Schematy bloku](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Wybierz plik schematu, który chcesz edytować, a następnie wybierz **Otwórz**.

    ![Plik schematu otwarte do edycji](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure pokazuje komunikat pomyślnie przekazana schematu.

## <a name="delete-schemas"></a>Usuwanie schematów

1. Wybierz **schematy** kafelka.

2. Po **schematy** zostanie otwarty blok, wybierz schemat do usunięcia.

3. Na **schematy** bloku, wybierz **usunąć**.

    ![Schematy bloku](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Aby upewnić się, że chcesz usunąć wybrany schemat, należy wybrać **tak**.

    ![Komunikat potwierdzający "Schematu delete"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    W **schematy** bloku odświeża listy schematu i nie zawiera już schemat, który zostanie usunięty.

    ![Twoje konto, z wyróżnioną pozycją "schematy" integracji](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw").  

