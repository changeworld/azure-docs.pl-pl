---
title: Szyfrowanie w usłudze Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Szyfrowanie w usłudze Azure Data Lake Storage Gen1 ułatwia ochronę danych, wdrażanie zasad zabezpieczeń przedsiębiorstwa i spełnianie wymagań dotyczących zgodności z przepisami. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878446"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Szyfrowanie danych w usłudze Azure Data Lake Storage Gen1

Szyfrowanie w usłudze Azure Data Lake Storage Gen1 ułatwia ochronę danych, wdrażanie zasad zabezpieczeń przedsiębiorstwa i spełnianie wymagań dotyczących zgodności z przepisami. Ten artykuł zawiera omówienie projektu i niektórych technicznych aspektów implementacji.

Data Lake Storage Gen1 obsługuje szyfrowanie danych zarówno w spoczynku, jak i podczas przesyłania. W przypadku danych w spoczynku usługa Data Lake Storage Gen1 obsługuje "domyślnie włączone", przezroczyste szyfrowanie. Poniżej przedstawiono bardziej szczegółowe wyjaśnienie tych terminów:

* **Domyślnie**włączone: Podczas tworzenia nowego konta Data Lake Storage Gen1 ustawienie domyślne włącza szyfrowanie. Następnie dane przechowywane w u źródła danych Lake Storage Gen1 są zawsze szyfrowane przed zapisaniem na nośniku trwałym. Takie działanie dotyczy wszystkich danych i nie można go zmienić po utworzeniu konta.
* **Przezroczysty:** Data Lake Storage Gen1 automatycznie szyfruje dane przed utrwalaniem i odszyfrowuje dane przed pobraniem. Szyfrowanie jest konfigurowane i zarządzane na poziomie konta Data Lake Storage Gen1 przez administratora. W interfejsach API dostępu do danych nie są wprowadzane żadne zmiany. W związku z tym żadne zmiany nie są wymagane w aplikacjach i usługach, które współdziałają z usługą Data Lake Storage Gen1 z powodu szyfrowania.

Przesyłane dane (znane również jako dane w ruchu) są również zawsze szyfrowane w umiaru danych Lake Storage Gen1. Oprócz tego, że dane są szyfrowane przed zapisaniem na nośniku trwałym, są również zawsze zabezpieczane podczas przesyłania przy użyciu protokołu HTTPS. HTTPS jest jedynym protokołem, który jest obsługiwany dla interfejsów REST usługi Data Lake Storage Gen1. Na poniższym diagramie pokazano, jak dane stają się szyfrowane w umiań magazynu usługi Data Lake:

![Diagram szyfrowania danych w umiań przechowywania usługi Data Lake](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Konfigurowanie szyfrowania za pomocą usługi Data Lake Storage Gen1

Szyfrowanie dla usługi Data Lake Storage Gen1 jest skonfigurowane podczas tworzenia konta i jest zawsze włączone domyślnie. Możesz samodzielnie zarządzać kluczami lub zezwolić na zarządzanie nimi w u źródła danych Lake Storage Gen1 (jest to wartość domyślna).

Aby uzyskać więcej informacji, zobacz [Wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Jak działa szyfrowanie w umiań przechowywania usługi Data Lake

Poniżej przedstawiono sposób zarządzania głównymi kluczami szyfrowania i wyjaśniono trzy różne typy kluczy, których można używać w szyfrowaniu danych dla usługi Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Główne klucze szyfrowania

Data Lake Storage Gen1 udostępnia dwa tryby zarządzania głównymi kluczami szyfrowania (MEK). Na razie załóżmy, że główny klucz szyfrowania jest kluczem najwyższego poziomu. Dostęp do głównego klucza szyfrowania jest wymagany do odszyfrowania wszelkich danych przechowywanych w programie Data Lake Storage Gen1.

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
|Czy można odwołać dostęp do usługi MEK dla usługi Data Lake Storage Gen1?|Nie|Tak. Możesz zarządzać listami kontroli dostępu w usłudze Key Vault i usuwać wpisy kontroli dostępu do tożsamości usługi dla usługi Data Lake Storage Gen1.|
|Czy można trwale usunąć główny klucz szyfrowania?|Nie|Tak. Jeśli usuniesz MEK z usługi Key Vault, dane na koncie Data Lake Storage Gen1 nie mogą być odszyfrowane przez nikogo, w tym usługę Data Lake Storage Gen1. <br><br> Jeśli przed usunięciem głównego klucza szyfrowania z usługi Key Vault jawnie utworzono jego kopię zapasową, to można go przywrócić i odzyskać dane. Jeśli jednak nie wykonano kopii zapasowej pliku MEK przed usunięciem go z usługi Key Vault, dane na koncie Usługi Data Lake Storage Gen1 nigdy nie można odszyfrować później.|


Poza różnicą dotyczącą sposobu zarządzania głównym kluczem szyfrowania i wystąpieniem usługi Key Vault, w którym ten klucz się znajduje, pozostałe elementy projektu są takie same dla obu trybów.

Podczas wybierania trybu głównych kluczy szyfrowania należy pamiętać o następujących kwestiach:

*   Podczas inicjowania obsługi administracyjnej konta Gen1 usługi Data Lake Storage Gen1 można wybrać, czy mają być używane klucze zarządzane przez klienta, czy klucze zarządzane usługą.
*   Po aprowizowaniu konta Usługi Data Lake Storage Gen1 nie można zmienić trybu.

### <a name="encryption-and-decryption-of-data"></a>Szyfrowanie i odszyfrowywanie danych

W projekcie szyfrowania danych używane są trzy typy kluczy. Poniższa tabela zawiera podsumowanie:

| Klucz                   | Skrót | Skojarzony z | Lokalizacja magazynu                             | Typ       | Uwagi                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Główny klucz szyfrowania | GKS          | Konto Data Lake Storage Gen1 | Usługa Key Vault                              | Asymetryczny | Może być zarządzany przez Data Lake Storage Gen1 lub Ciebie.                                                              |
| Klucz szyfrowania danych   | KSD          | Konto Data Lake Storage Gen1 | Magazyn trwały zarządzany przez usługę Data Lake Storage Gen1 | Symetryczny  | Klucz szyfrowania danych jest szyfrowany przy użyciu głównego klucza szyfrowania. Na nośniku trwałym jest zapisywany zaszyfrowany klucz szyfrowania danych. |
| Klucz szyfrowania bloków  | KSB          | Blok danych | Brak                                         | Symetryczny  | Klucz szyfrowania bloków jest tworzony na podstawie klucza szyfrowania danych i bloku danych.                                                      |

Poniższy diagram przedstawia te koncepcje:

![Klucze używane do szyfrowania danych](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgorytm stosowany w przypadku odszyfrowywania pliku:
1.  Sprawdź, czy identyfikator DEK dla konta Data Lake Storage Gen1 jest buforowany i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Dotyczy każdego bloku danych w pliku:
    - Odczytaj zaszyfrowany blok danych z magazynu trwałego.
    - Wygeneruj klucz szyfrowania bloków na podstawie klucza szyfrowania danych i zaszyfrowanego bloku danych.
    - Użyj klucza szyfrowania bloków w celu odszyfrowania danych.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgorytm stosowany w przypadku szyfrowania bloku danych:
1.  Sprawdź, czy identyfikator DEK dla konta Data Lake Storage Gen1 jest buforowany i gotowy do użycia.
    - Jeśli nie, odczytaj zaszyfrowany klucz szyfrowania danych z magazynu trwałego i prześlij go do usługi Key Vault w celu odszyfrowania. Odszyfrowany klucz szyfrowania danych zapisz w pamięci podręcznej. Jest on teraz gotowy do użycia.
2.  Wygeneruj unikatowy klucz szyfrowania bloków dla bloku danych na podstawie klucza szyfrowania danych.
3.  Zaszyfruj blok danych przy użyciu klucza szyfrowania bloków, korzystając z algorytmu AES-256.
4.  Zapisz zaszyfrowany blok danych w magazynie trwałym.

> [!NOTE] 
> Klucz szyfrowania danych jest zawsze przechowywany jako zaszyfrowany za pomocą głównego klucza szyfrowania na nośniku trwałym lub buforowany w pamięci.

## <a name="key-rotation"></a>Wymiana kluczy

W przypadku korzystania z kluczy zarządzanych przez klienta można wymienić główny klucz szyfrowania. Aby dowiedzieć się, jak skonfigurować konto Data Lake Storage Gen1 przy za pomocą kluczy zarządzanych przez klienta, zobacz [Wprowadzenie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Wymagania wstępne

Podczas konfigurowania konta Data Lake Storage Gen1, wybrano użycie własnych kluczy. Po utworzeniu konta ta opcja nie może zostać zmieniona. W poniższych krokach przyjęto, że używasz kluczy zarządzanych przez klienta (tzn. wybrano własne klucze z usługi Key Vault).

Należy zauważyć, że jeśli używasz domyślnych opcji szyfrowania, dane są zawsze szyfrowane przy użyciu kluczy zarządzanych przez Data Lake Storage Gen1. W tej opcji nie masz możliwości obracania klawiszy, ponieważ są one zarządzane przez Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Jak obrócić MEK w Data Lake Storage Gen1

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Przejdź do wystąpienia usługi Key Vault, w które przechowywane są klucze skojarzone z kontem Data Lake Storage Gen1. Wybierz pozycję **Klucze**.

    ![Zrzut ekranu usługi Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Wybierz klucz skojarzony z kontem Data Lake Storage Gen1 i utwórz nową wersję tego klucza. Należy zauważyć, że Data Lake Storage Gen1 obecnie obsługuje tylko rotacji klucza do nowej wersji klucza. Wymiana na inny klucz nie jest obsługiwana.

   ![Zrzut ekranu okna Klucze z wyróżnionym przyciskiem Nowa wersja](./media/data-lake-store-encryption/keynewversion.png)

4. Przejdź do konta Data Lake Storage Gen1 i wybierz pozycję **Szyfrowanie**.

   ![Zrzut ekranu przedstawiający okno konta Data Lake Storage Gen1 z wyróżnionym połączenie szyfrowania](./media/data-lake-store-encryption/select-encryption.png)

5. Zostanie wyświetlony komunikat informujący o dostępności nowej wersji klucza. Kliknij pozycję **Wymień klucz**, aby zaktualizować klucz do nowej wersji.

   ![Zrzut ekranu przedstawiający okno Data Lake Storage Gen1 z wyróżnioną wiadomością i klawiszem Obróć](./media/data-lake-store-encryption/rotatekey.png)

Ta operacja powinna zająć mniej niż dwie minuty i nie powinna powodować żadnego przestoju. Po zakończeniu operacji jest używana nowa wersja klucza.

> [!IMPORTANT]
> Po zakończeniu operacji rotacji kluczy stara wersja klucza nie jest już aktywnie używana do szyfrowania danych.  Jednak w rzadkich przypadkach wystąpienia nieoczekiwanych awarii, które mają wpływ nawet na nadmiarowe kopie danych, dane można przywrócić z kopii zapasowej nadal używającej starego klucza. Aby zapewnić dostępność danych w tych rzadkich przypadkach, należy zachować kopię poprzedniej wersji klucza szyfrowania. Zobacz [wskazówki dotyczące odzyskiwania po awarii dla danych w u źródła danych Lake Storage Gen1,](data-lake-store-disaster-recovery-guidance.md) aby uzyskać najlepsze rozwiązania dotyczące planowania odzyskiwania po awarii. 