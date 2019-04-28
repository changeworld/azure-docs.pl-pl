---
title: Szyfrowanie w usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Szyfrowanie w usłudze Azure Data Lake magazynu Gen1 pomaga chronić dane, implementować zasady bezpieczeństwa w przedsiębiorstwie i spełniać wymagania zgodności z przepisami. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878446"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Szyfrowanie danych w usłudze Azure Data Lake magazynu Gen1

Szyfrowanie w usłudze Azure Data Lake magazynu Gen1 pomaga chronić dane, implementować zasady bezpieczeństwa w przedsiębiorstwie i spełniać wymagania zgodności z przepisami. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.

Data Lake Storage Gen1 obsługuje szyfrowanie danych zarówno magazynowanych, jak i podczas przesyłania. Dla danych magazynowanych usługa Data Lake Storage Gen1 obsługuje "na domyślnie" przezroczyste szyfrowanie. Poniżej przedstawiono bardziej szczegółowe wyjaśnienie tych terminów:

* **Na domyślnie**: Podczas tworzenia nowego konta Data Lake Storage Gen1 domyślne ustawienie włącza szyfrowanie. Dzięki temu dane są przechowywane w Data Lake Storage Gen1 są zawsze szyfrowane jeszcze przed zapisaniem na nośniku trwałym. Takie działanie dotyczy wszystkich danych i nie można go zmienić po utworzeniu konta.
* **Przezroczysty**: Data Lake Storage Gen1 automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje przed pobraniem. Szyfrowanie jest konfigurowane i zarządzane na poziomie konta Data Lake Storage Gen1 przez administratora. W interfejsach API dostępu do danych nie są wprowadzane żadne zmiany. W związku z tym żadne zmiany nie są wymagane w aplikacjach i usługach, współpracujące z usługą Data Lake Storage Gen1 z powodu szyfrowania.

Dane przesyłane (znany także jako dane w ruchu) również są zawsze szyfrowane w Data Lake Storage Gen1. Oprócz tego, że dane są szyfrowane przed zapisaniem na nośniku trwałym, są również zawsze zabezpieczane podczas przesyłania przy użyciu protokołu HTTPS. Protokół HTTPS jest jedynym protokołem obsługiwanym przez interfejsy REST Gen1 magazynu jeziora danych. Na poniższym diagramie przedstawiono sposób szyfrowania danych w Data Lake Storage Gen1:

![Diagram szyfrowania danych w Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Konfigurowanie szyfrowania przy użyciu Data Lake Storage Gen1

Szyfrowanie dla usługi Data Lake Storage Gen1 konfiguruje się podczas tworzenia konta i jest zawsze włączona domyślnie. Możesz samodzielnie zarządzać kluczami lub zezwolić Data Lake Storage Gen1 do zarządzania nimi dla Ciebie (jest to wartość domyślna).

Aby uzyskać więcej informacji, zobacz [Wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Jak działa szyfrowanie w Data Lake Storage Gen1

W poniższej sekcji omówiono sposób zarządzania głównymi kluczami szyfrowania, a ponadto wyjaśniono trzy różne typy kluczy, które służy do szyfrowania danych dla programu Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Główne klucze szyfrowania

Data Lake Storage Gen1 udostępnia dwa tryby zarządzania głównych kluczy szyfrowania (głównymi kluczami szyfrowania). Na razie załóżmy, że główny klucz szyfrowania jest kluczem najwyższego poziomu. Aby odszyfrować dowolne dane przechowywane w Data Lake Storage Gen1 wymagany jest dostęp do głównego klucza szyfrowania.

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
|Kto jest właścicielem wystąpienia usługi Key Vault i głównego klucza szyfrowania?|Usługa Data Lake Storage Gen1|Ty jesteś właścicielem wystąpienia usługi Key Vault, które znajduje się w Twojej subskrypcji platformy Azure. Głównym kluczem szyfrowania w usłudze Key Vault można zarządzać programowo lub sprzętowo.|
|Czy można odwołać dostęp usługi Data Lake Storage Gen1 do głównego klucza szyfrowania?|Nie|Tak. Możesz zarządzać listami kontroli dostępu w usłudze Key Vault i usuwać pozycje kontroli dostępu do tożsamości usługi dla usługi Data Lake Storage Gen1.|
|Czy można trwale usunąć główny klucz szyfrowania?|Nie|Tak. Jeśli usuniesz główny klucz szyfrowania z usługi Key Vault, dane konta Data Lake Storage Gen1 nie można odszyfrować przez nikogo, łącznie z usługą Data Lake Storage Gen1. <br><br> Jeśli przed usunięciem głównego klucza szyfrowania z usługi Key Vault jawnie utworzono jego kopię zapasową, to można go przywrócić i odzyskać dane. Jednak jeśli nie utworzono kopię zapasową głównego klucza szyfrowania przed usunięciem go z usługi Key Vault, dane konta Data Lake Storage Gen1 może nigdy nie być odszyfrowane.|


Poza różnicą dotyczącą sposobu zarządzania głównym kluczem szyfrowania i wystąpieniem usługi Key Vault, w którym ten klucz się znajduje, pozostałe elementy projektu są takie same dla obu trybów.

Podczas wybierania trybu głównych kluczy szyfrowania należy pamiętać o następujących kwestiach:

*   Możesz wybrać, czy klienta kluczami zarządzanymi przez usługę podczas aprowizacji konta Data Lake Storage Gen1.
*   Po aprowizacji konta Data Lake Storage Gen1 nie można zmienić trybu.

### <a name="encryption-and-decryption-of-data"></a>Szyfrowanie i odszyfrowywanie danych

W projekcie szyfrowania danych używane są trzy typy kluczy. Poniższa tabela zawiera podsumowanie:

| Klucz                   | Skrót | Skojarzony z | Lokalizacja magazynu                             | Type       | Uwagi                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Główny klucz szyfrowania | GKS          | Konta Data Lake Storage Gen1 | Usługa Key Vault                              | Asymetryczny | Można nim zarządzać, Data Lake Storage Gen1 lub.                                                              |
| Klucz szyfrowania danych   | KSD          | Konta Data Lake Storage Gen1 | Magazyn trwały zarządzany przez usługę Data Lake Storage Gen1 | Symetryczny  | Klucz szyfrowania danych jest szyfrowany przy użyciu głównego klucza szyfrowania. Na nośniku trwałym jest zapisywany zaszyfrowany klucz szyfrowania danych. |
| Klucz szyfrowania bloków  | KSB          | Blok danych | Brak                                         | Symetryczny  | Klucz szyfrowania bloków jest tworzony na podstawie klucza szyfrowania danych i bloku danych.                                                      |

Poniższy diagram przedstawia te koncepcje:

![Klucze używane do szyfrowania danych](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgorytm stosowany w przypadku odszyfrowywania pliku:
1.  Sprawdź, czy klucz szyfrowania danych dla konta Data Lake Storage Gen1 pamięci podręcznej i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Dotyczy każdego bloku danych w pliku:
    - Odczytaj zaszyfrowany blok danych z magazynu trwałego.
    - Wygeneruj klucz szyfrowania bloków na podstawie klucza szyfrowania danych i zaszyfrowanego bloku danych.
    - Użyj klucza szyfrowania bloków w celu odszyfrowania danych.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgorytm stosowany w przypadku szyfrowania bloku danych:
1.  Sprawdź, czy klucz szyfrowania danych dla konta Data Lake Storage Gen1 pamięci podręcznej i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Wygeneruj unikatowy klucz szyfrowania bloków dla bloku danych na podstawie klucza szyfrowania danych.
3.  Zaszyfruj blok danych przy użyciu klucza szyfrowania bloków, korzystając z algorytmu AES-256.
4.  Zapisz zaszyfrowany blok danych w magazynie trwałym.

> [!NOTE] 
> Klucz szyfrowania danych jest zawsze przechowywany jako zaszyfrowany za pomocą głównego klucza szyfrowania na nośniku trwałym lub buforowany w pamięci.

## <a name="key-rotation"></a>Wymiana kluczy

W przypadku korzystania z kluczy zarządzanych przez klienta można wymienić główny klucz szyfrowania. Aby dowiedzieć się, jak skonfigurować konta Data Lake Storage Gen1 za pomocą kluczy zarządzanych przez klienta, zobacz [wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Wymagania wstępne

Po skonfigurowaniu konta Data Lake Storage Gen1 wybrano korzystania z własnych kluczy. Po utworzeniu konta ta opcja nie może zostać zmieniona. W poniższych krokach przyjęto, że używasz kluczy zarządzanych przez klienta (tzn. wybrano własne klucze z usługi Key Vault).

Należy pamiętać, że jeśli używasz domyślnych opcji szyfrowania, Twoje dane są zawsze szyfrowane za pomocą kluczy zarządzanych przez Data Lake Storage Gen1. W przypadku tej opcji nie ma możliwości wymiany kluczy, ponieważ są one zarządzane przez Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Jak wymienić główny klucz szyfrowania w Data Lake Storage Gen1

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do wystąpienia usługi Key Vault, który przechowuje klucze skojarzone z kontem usługi Data Lake Storage Gen1. Wybierz pozycję **Klucze**.

    ![Zrzut ekranu usługi Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Wybierz klucz skojarzony z kontem usługi Data Lake Storage Gen1 i Utwórz nową wersję tego klucza. Należy pamiętać, że Data Lake Storage Gen1 obecnie obsługuje wyłącznie wymianę kluczy do nowej wersji klucza. Wymiana na inny klucz nie jest obsługiwana.

   ![Zrzut ekranu okna Klucze z wyróżnionym przyciskiem Nowa wersja](./media/data-lake-store-encryption/keynewversion.png)

4. Przejdź do konta Data Lake Storage Gen1, a następnie wybierz pozycję **szyfrowania**.

   ![Zrzut ekranu z Data Lake Storage Gen1 okna konta z wyróżnioną pozycją szyfrowanie](./media/data-lake-store-encryption/select-encryption.png)

5. Zostanie wyświetlony komunikat informujący o dostępności nowej wersji klucza. Kliknij pozycję **Wymień klucz**, aby zaktualizować klucz do nowej wersji.

   ![Zrzut ekranu z Data Lake Storage Gen1 okno z komunikatem i wyróżnioną pozycją Wymień klucz](./media/data-lake-store-encryption/rotatekey.png)

Ta operacja powinna zająć mniej niż dwie minuty i nie powinna powodować żadnego przestoju. Po zakończeniu operacji jest używana nowa wersja klucza.

> [!IMPORTANT]
> Po zakończeniu operacji rotacji kluczy stara wersja klucza nie jest już aktywnie używana do szyfrowania danych.  Jednak w rzadkich przypadkach wystąpienia nieoczekiwanych awarii, które mają wpływ nawet na nadmiarowe kopie danych, dane można przywrócić z kopii zapasowej nadal używającej starego klucza. Aby zapewnić dostępność danych w tych rzadkich przypadkach, należy zachować kopię poprzedniej wersji klucza szyfrowania. Zobacz [wskazówki dotyczące odzyskiwania po awarii dla danych w Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) dla najlepsze rozwiązania w zakresie planowania odzyskiwania po awarii. 