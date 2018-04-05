---
title: Szyfrowanie w usłudze Azure Data Lake Store | Microsoft Docs
description: Szyfrowanie w usłudze Azure Data Lake Store pomaga chronić dane, implementować zasady bezpieczeństwa w przedsiębiorstwie i spełniać prawne wymagania dotyczące zgodności. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.
services: data-lake-store
documentationcenter: ''
author: esung22
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: 53d5f413f58cea7bc8eab081d46eff2ab83e7ecb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Szyfrowanie danych w usłudze Azure Data Lake Store

Szyfrowanie w usłudze Azure Data Lake Store pomaga chronić dane, implementować zasady bezpieczeństwa w przedsiębiorstwie i spełniać prawne wymagania dotyczące zgodności. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.

Usługa Data Lake Store obsługuje szyfrowanie danych zarówno magazynowanych, jak i przesyłanych. W przypadku danych magazynowanych usługa Data Lake Store obsługuje domyślnie włączone szyfrowanie przezroczyste. Poniżej przedstawiono bardziej szczegółowe wyjaśnienie tych terminów:

* **Domyślnie włączone**: po utworzeniu nowego konta usługi Data Lake Store domyślne ustawienie włącza szyfrowanie. Dzięki temu dane przechowywane w usłudze Data Lake Store są zawsze szyfrowane jeszcze przed zapisaniem na nośniku trwałym. Takie działanie dotyczy wszystkich danych i nie można go zmienić po utworzeniu konta.
* **Przezroczyste**: usługa Data Lake Store automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje przed pobraniem. Szyfrowanie jest konfigurowane i zarządzane na poziomie usługi Data Lake Store przez administratora. W interfejsach API dostępu do danych nie są wprowadzane żadne zmiany. W związku z tym z powodu szyfrowania nie ma konieczności wprowadzania żadnych zmian w aplikacjach i usługach, które współdziałają z usługą Data Lake Store.

Dane przesyłane (inaczej dane w ruchu) również są zawsze szyfrowane w usłudze Data Lake Store. Oprócz tego, że dane są szyfrowane przed zapisaniem na nośniku trwałym, są również zawsze zabezpieczane podczas przesyłania przy użyciu protokołu HTTPS. Protokół HTTPS jest jedynym protokołem obsługiwanym przez interfejsy REST usługi Data Lake Store. Na poniższym diagramie przedstawiono sposób szyfrowania danych w usłudze Data Lake Store:

![Diagram szyfrowania danych w usłudze Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Konfigurowanie szyfrowania przy użyciu usługi Data Lake Store

Szyfrowanie w usłudze Data Lake Store konfiguruje się podczas tworzenia konta i zawsze jest domyślnie włączone. Kluczami możesz zarządzać samodzielnie lub zezwolić na to usłudze Data Lake Store (jest to opcja domyślna).

Aby uzyskać więcej informacji, zobacz [Wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>Jak działa szyfrowanie w usłudze Data Lake Store

W poniższej sekcji omówiono sposób zarządzania głównymi kluczami szyfrowania oraz opisano trzy różne typy kluczy, których można używać na potrzeby szyfrowania danych w usłudze Data Lake Store.

### <a name="master-encryption-keys"></a>Główne klucze szyfrowania

Usługa Data Lake Store udostępnia dwa tryby zarządzania głównymi kluczami szyfrowania. Na razie załóżmy, że główny klucz szyfrowania jest kluczem najwyższego poziomu. Aby móc odszyfrować dowolne dane przechowywane w usłudze Data Lake Store, wymagany jest dostęp do głównego klucza szyfrowania.

Oto dwa tryby zarządzania głównym kluczem szyfrowania:

*   Klucze zarządzane przez usługę
*   Klucze zarządzane przez klienta

W obu trybach główny klucz szyfrowania jest zabezpieczony dzięki przechowywaniu go w usłudze Azure Key Vault. Key Vault to w pełni zarządzana, wysoce bezpieczna usługa platformy Azure, która może służyć do ochrony kluczy kryptograficznych. Aby uzyskać więcej informacji, zobacz [Key Vault](https://azure.microsoft.com/services/key-vault).

Oto krótkie porównanie możliwości oferowanych przez dwa tryby zarządzania głównymi kluczami szyfrowania.

|  | Klucze zarządzane przez usługę | Klucze zarządzane przez klienta |
| --- | --- | --- |
|W jaki sposób przechowywane są dane?|Są zawsze szyfrowane przed zapisaniem.|Są zawsze szyfrowane przed zapisaniem.|
|Gdzie jest przechowywany główny klucz szyfrowania?|Usługa Key Vault|Usługa Key Vault|
|Czy jakiekolwiek klucze szyfrowania są przechowywane poza usługą Key Vault? |Nie|Nie|
|Czy można pobrać główny klucz szyfrowania za pomocą usługi Key Vault?|Nie. Po umieszczeniu głównego klucza szyfrowania w usłudze Key Vault można go używać tylko do szyfrowania i odszyfrowywania.|Nie. Po umieszczeniu głównego klucza szyfrowania w usłudze Key Vault można go używać tylko do szyfrowania i odszyfrowywania.|
|Kto jest właścicielem wystąpienia usługi Key Vault i głównego klucza szyfrowania?|Usługa Data Lake Store|Ty jesteś właścicielem wystąpienia usługi Key Vault, które znajduje się w Twojej subskrypcji platformy Azure. Głównym kluczem szyfrowania w usłudze Key Vault można zarządzać programowo lub sprzętowo.|
|Czy można odwołać dostęp usługi Data Lake Store do głównego klucza szyfrowania?|Nie|Tak. Możesz zarządzać listami kontroli dostępu w usłudze Key Vault i usuwać pozycje kontroli dostępu dla tożsamości usługi w ramach usługi Data Lake Store.|
|Czy można trwale usunąć główny klucz szyfrowania?|Nie|Tak. Jeśli usuniesz główny klucz szyfrowania z usługi Key Vault, dane na koncie usługi Data Lake Store nie będą mogły być odszyfrowane przez nikogo, łącznie z usługą Data Lake Store. <br><br> Jeśli przed usunięciem głównego klucza szyfrowania z usługi Key Vault jawnie utworzono jego kopię zapasową, to można go przywrócić i odzyskać dane. Jednak jeśli przed usunięciem głównego klucza szyfrowania z usługi Key Vault nie utworzono jego kopii zapasowej, dane na koncie usługi Data Lake Store nigdy już nie będą mogły być odszyfrowane.|


Poza różnicą dotyczącą sposobu zarządzania głównym kluczem szyfrowania i wystąpieniem usługi Key Vault, w którym ten klucz się znajduje, pozostałe elementy projektu są takie same dla obu trybów.

Podczas wybierania trybu głównych kluczy szyfrowania należy pamiętać o następujących kwestiach:

*   Podczas aprowizacji konta usługi Data Lake Store można wybrać między kluczami zarządzanymi przez klienta i kluczami zarządzanymi przez usługę.
*   Po aprowizacji konta usługi Data Lake Store nie można zmienić trybu.

### <a name="encryption-and-decryption-of-data"></a>Szyfrowanie i odszyfrowywanie danych

W projekcie szyfrowania danych używane są trzy typy kluczy. Poniższa tabela zawiera podsumowanie:

| Klucz                   | Skrót | Skojarzony z | Lokalizacja magazynu                             | Typ       | Uwagi                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Główny klucz szyfrowania | GKS          | Konto usługi Data Lake Store | Usługa Key Vault                              | Asymetryczny | Może nim zarządzać usługa Data Lake Store lub można to robić samodzielnie.                                                              |
| Klucz szyfrowania danych   | KSD          | Konto usługi Data Lake Store | Magazyn trwały zarządzany przez usługę Data Lake Store | Symetryczny  | Klucz szyfrowania danych jest szyfrowany przy użyciu głównego klucza szyfrowania. Na nośniku trwałym jest zapisywany zaszyfrowany klucz szyfrowania danych. |
| Klucz szyfrowania bloków  | KSB          | Blok danych | Brak                                         | Symetryczny  | Klucz szyfrowania bloków jest tworzony na podstawie klucza szyfrowania danych i bloku danych.                                                      |

Poniższy diagram przedstawia te koncepcje:

![Klucze używane do szyfrowania danych](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgorytm stosowany w przypadku odszyfrowywania pliku:
1.  Sprawdź, czy klucz szyfrowania danych dla konta usługi Data Lake Store jest zapisany w pamięci podręcznej i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Dotyczy każdego bloku danych w pliku:
    - Odczytaj zaszyfrowany blok danych z magazynu trwałego.
    - Wygeneruj klucz szyfrowania bloków na podstawie klucza szyfrowania danych i zaszyfrowanego bloku danych.
    - Użyj klucza szyfrowania bloków w celu odszyfrowania danych.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgorytm stosowany w przypadku szyfrowania bloku danych:
1.  Sprawdź, czy klucz szyfrowania danych dla konta usługi Data Lake Store jest zapisany w pamięci podręcznej i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Wygeneruj unikatowy klucz szyfrowania bloków dla bloku danych na podstawie klucza szyfrowania danych.
3.  Zaszyfruj blok danych przy użyciu klucza szyfrowania bloków, korzystając z algorytmu AES-256.
4.  Zapisz zaszyfrowany blok danych w magazynie trwałym.

> [!NOTE] 
> Klucz szyfrowania danych jest zawsze przechowywany jako zaszyfrowany za pomocą głównego klucza szyfrowania na nośniku trwałym lub buforowany w pamięci.

## <a name="key-rotation"></a>Wymiana kluczy

W przypadku korzystania z kluczy zarządzanych przez klienta można wymienić główny klucz szyfrowania. Aby dowiedzieć się, jak skonfigurować konto usługi Data Lake Store korzystające z kluczy zarządzanych przez klienta, zobacz [Wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Wymagania wstępne

Podczas konfigurowania konta usługi Data Lake Store wybrano opcję użycia własnych kluczy. Po utworzeniu konta ta opcja nie może zostać zmieniona. W poniższych krokach przyjęto, że używasz kluczy zarządzanych przez klienta (tzn. wybrano własne klucze z usługi Key Vault).

Pamiętaj, że jeśli użyjesz domyślnych opcji szyfrowania, dane będą zawsze szyfrowane za pomocą kluczy zarządzanych przez usługę Data Lake Store. W przypadku tej opcji nie ma możliwości wymiany kluczy, ponieważ są one zarządzane przez usługę Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Jak wymienić główny klucz szyfrowania w usłudze Data Lake Store

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do wystąpienia usługi Key Vault, w którym są przechowywane klucze skojarzone z Twoim kontem usługi Data Lake Store. Wybierz pozycję **Klucze**.

    ![Zrzut ekranu usługi Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Wybierz klucz skojarzony z kontem usługi Data Lake Store i utwórz nową wersję tego klucza. Pamiętaj, że usługa Data Lake Store obecnie obsługuje wymianę tylko na nową wersję klucza. Wymiana na inny klucz nie jest obsługiwana.

   ![Zrzut ekranu okna Klucze z wyróżnionym przyciskiem Nowa wersja](./media/data-lake-store-encryption/keynewversion.png)

4.  Przejdź do konta magazynu usługi Data Lake Store i wybierz pozycję **Szyfrowanie**.

    ![Zrzut ekranu okna konta magazynu usługi Data Lake Store z wyróżnioną pozycją Szyfrowanie](./media/data-lake-store-encryption/select-encryption.png)

5.  Zostanie wyświetlony komunikat informujący o dostępności nowej wersji klucza. Kliknij pozycję **Wymień klucz**, aby zaktualizować klucz do nowej wersji.

    ![Zrzut ekranu okna usługi Data Lake Store z komunikatem i wyróżnioną pozycją Wymień klucz](./media/data-lake-store-encryption/rotatekey.png)

Ta operacja powinna zająć mniej niż dwie minuty i nie powinna powodować żadnego przestoju. Po zakończeniu operacji jest używana nowa wersja klucza.
