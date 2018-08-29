---
title: Dodawanie schematów na potrzeby weryfikacji danych XML — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie schematów, sprawdzania poprawności XML dokumenty w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 07/29/2016
ms.openlocfilehash: e03346da1c2b77f885c39d5329f990684979c56e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123077"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Walidacja danych XML przy użyciu schematów w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Schematy upewnij się, że dokumentów XML, które otrzymujesz są prawidłowe i zawiera oczekiwane dane wstępnie zdefiniowany format. Schematy również okazać pomocny sprawdzania poprawności komunikatów, które są wymieniane w scenariuszu B2B.

## <a name="add-a-schema"></a>Dodawanie schematu

1. W witrynie Azure portal wybierz **wszystkich usług**.

    ![Witryny Azure portal, "Wszystkie usługi"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. W polu wyszukiwania filtr wprowadź **integracji**i wybierz **kont integracji** z listy wyników.

    ![Filtruj pola wyszukiwania](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Wybierz **konta integracji** której chcesz dodać schematu.

    ![Lista kont integracji](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Wybierz **schematów** kafelka.

    ![Konto integracji przykład, "Schematy"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Dodać plik schematu, które jest mniejszy niż 2 MB

1. W **schematów** bloku, który otworzy (z poprzednich kroków), wybierz opcję **Dodaj**.

    ![Schematy bloku "Dodaj".](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Wprowadź nazwę dla schematu. Przekaż plik schematu, wybierając ikonę folderu obok pozycji **schematu** pole. Po zakończeniu procesu przekazywania wybierz **OK**.

    ![Zrzut ekranu przedstawiający "Dodaj Schema", przy użyciu małych "pliku" wyróżniony](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Dodać plik schematu, które jest większe niż 2 MB (maksymalnie 8 MB)

Te kroki różnią się w zależności na poziomie dostępu do kontenera obiektów blob: **publicznych** lub **bez dostępu anonimowego**.

**Aby określić, ten poziom dostępu**

1.  Otwórz **Eksplorator usługi Azure Storage**. 

2.  W obszarze **kontenery obiektów Blob**, wybierz kontener obiektów blob, które chcesz. 

3.  Wybierz **zabezpieczeń**, **poziom dostępu**.

Jeśli poziom dostępu zabezpieczeń obiektu blob jest **publicznych**, wykonaj następujące kroki.

![Eksplorator usługi Azure Storage, za pomocą "Kontenery obiektów Blob", "Zabezpieczenia" i "Publicznej" wyróżniony](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Przekaż schemat do konta magazynu i skopiuj identyfikator URI.

    ![Konto magazynu, za pomocą identyfikatora URI wyróżniony](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. W **schemat Dodaj**, wybierz opcję **plików o dużym rozmiarze**i podaj identyfikator URI w **identyfikator URI zawartości** pola tekstowego.

    ![Schematy, za pomocą przycisku "Dodaj" i "Dużych plików" wyróżniony](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Jeśli poziom dostępu zabezpieczeń obiektu blob jest **bez dostępu anonimowego**, wykonaj następujące kroki.

![Eksplorator usługi Azure Storage, za pomocą "Kontenery obiektów Blob", "Zabezpieczenia" i "Brak dostępu anonimowego" wyróżniony](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Przekaż schemat do konta magazynu.

    ![Konto magazynu](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Generowanie sygnatury dostępu współdzielonego dla schematu.

    ![Konto magazynu, z kartą sygnatur dostępu współdzielonego wyróżniony](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. W **schemat Dodaj**, wybierz opcję **plików o dużym rozmiarze**i podaj identyfikatora URI sygnatury dostępu współdzielonego w **identyfikator URI zawartości** pola tekstowego.

    ![Schematy, za pomocą przycisku "Dodaj" i "Dużych plików" wyróżniony](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. W **schematów** bloku konta usługi integracji, powinien być widoczny nowo dodanych schematu.

    ![Konta integracji z "Schematy" i nowego schematu wyróżniony](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Edytowanie schematów

1. Wybierz **schematów** kafelka.

2. Po **schematów** zostanie otwarty blok, wybierz schemat, który chcesz edytować.

3. Na **schematów** bloku wybierz **Edytuj**.

    ![Blok schematów](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Wybierz plik schematu, który chcesz edytować, a następnie wybierz **Otwórz**.

    ![Plik schematu Otwórz do edycji](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Usługa Azure przedstawiono wiadomości, które pomyślnie przekazano schemat.

## <a name="delete-schemas"></a>Usuwanie schematów

1. Wybierz **schematów** kafelka.

2. Po **schematów** zostanie otwarty blok, wybierz schemat do usunięcia.

3. Na **schematów** bloku wybierz **Usuń**.

    ![Blok schematów](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Aby upewnić się, że chcesz usunąć wybrany schemat, wybierz opcję **tak**.

    !["Usuwanie schematu" komunikat z potwierdzeniem](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    W **schematów** bloku, listy schematu odświeża i nie zawiera już schemat, który został usunięty.

    ![Twoje konto z wyróżnioną pozycją "schematy" integracji](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Kolejne kroki
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw").  

