---
title: Konfigurowanie magazynu VFXT usługi Avere — platforma Azure
description: Jak dodać system pamięci masowej zaplecza do usługi Avere vFXT for Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252612"
---
# <a name="configure-storage"></a>Konfigurowanie magazynu

Ten krok konfiguruje system pamięci masowej zaplecza dla klastra vFXT.

> [!TIP]
> Jeśli utworzono nowy kontener obiektów blob platformy Azure wraz z klastrem VFXT Avere, ten kontener jest już skonfigurowany i gotowy do użycia.

Postępuj zgodnie z tymi instrukcjami, jeśli nie utworzysz nowego kontenera obiektów Blob z klastrem lub jeśli chcesz dodać dodatkowy sprzęt lub system magazynu w chmurze.

Istnieją dwa główne zadania:

1. [Utwórz podstawowy filer](#create-a-core-filer), który łączy klaster vFXT z istniejącym systemem magazynowania lub kontenerem konta usługi Azure Storage.

1. [Utwórz skrzyżowanie obszaru nazw,](#create-a-junction)które definiuje ścieżkę, którą będą instalować klienci.

W tych krokach za pomocą Panelu sterowania Avere. Przeczytaj [dostęp do klastra vFXT,](avere-vfxt-cluster-gui.md) aby dowiedzieć się, jak go używać.

## <a name="create-a-core-filer"></a>Tworzenie głównego filera

"Core filer" to termin vFXT dla systemu pamięci masowej zaplecza. Pamięć masowa może być sprzętowym urządzeniem NAS, takim jak NetApp lub Isilon, lub może być magazynem obiektów w chmurze. Więcej informacji na temat głównych filerów można znaleźć w [przewodniku po ustawieniach klastra Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Aby dodać podstawowy filer, wybierz jeden z dwóch głównych typów głównych filerów:

* [Plik podstawowy NAS](#nas-core-filer) - opisuje, jak dodać plik rdzeń NAS
* [Filer z chmury usługi Azure Storage](#azure-blob-storage-cloud-core-filer) — opisuje sposób dodawania kontenera magazynu obiektów Blob platformy Azure jako filera z rdzeniem chmury

### <a name="nas-core-filer"></a>Plik podstawowy NAS

Głównym filerem NAS może być lokalne urządzenie NetApp lub Isilon lub punkt końcowy NAS w chmurze. System pamięci masowej musi mieć niezawodne szybkie połączenie z klastrem VFXT Avere — na przykład połączenie 1GBps ExpressRoute (nie sieć VPN) — i musi zapewniać rootowi klastra używany eksport nas.

Wykonaj następujące kroki, aby dodać podstawowy filer NAS:

1. W Panelu sterowania Avere kliknij kartę **Ustawienia** u góry.

1. Po lewej stronie kliknij **pozycję Core Filer** > **Manage Core Filers** .

1. Kliknij przycisk **Utwórz**.

   ![Zrzut ekranu przedstawiający stronę Dodaj nowy podstawowy filer z kursorem nad przyciskiem Utwórz](media/avere-vfxt-add-core-filer-start.png)

1. Wypełnij wymagane informacje w kreatorze:

   * Nazwij swojego głównego filera.
   * Podaj w pełni kwalifikowaną nazwę domeny (FQDN), jeśli jest dostępna. W przeciwnym razie podaj adres IP lub nazwę hosta, która jest rozpoznawana jako podstawowy filer.
   * Wybierz klasę filera z listy. Jeśli nie masz pewności, wybierz pozycję **Inne**.

     ![Zrzut ekranu przedstawiający stronę Dodaj nowy podstawowy filer z podstawową nazwą filera i w pełni kwalifikowaną nazwą domeny](media/avere-vfxt-add-core-filer.png)
  
   * Kliknij **przycisk Dalej** i wybierz zasadę pamięci podręcznej.
   * Kliknij **pozycję Dodaj plik .**
   * Aby uzyskać bardziej szczegółowe informacje, zobacz [Dodawanie nowego pliku serwera NAS](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) core w przewodniku po ustawieniach klastra Avere.

Następnie przejdź do [pozycji Utwórz połączenie](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Podstawowy filer usługi Azure Blob Storage

Aby użyć magazynu obiektów Blob platformy Azure jako magazynu zaplecza klastra vFXT, potrzebujesz pustego kontenera, aby dodać go jako głównego filera.

Dodanie magazynu obiektów blob do klastra wymaga następujących zadań:

* Tworzenie konta magazynu (krok 1, poniżej)
* Tworzenie pustego kontenera obiektów blob (kroki 2-3)
* Dodawanie klucza dostępu do magazynu jako poświadczenia chmury dla klastra vFXT (kroki 4-6)
* Dodawanie kontenera obiektów blob jako głównego filera dla klastra vFXT (kroki 7–9)
* Tworzenie połączenia obszaru nazw używanego przez klientów do instalowania głównego filera[(Tworzenie połączenia](#create-a-junction), tak samo zarówno dla sprzętu, jak i magazynu w chmurze)

> [!TIP]
> Jeśli utworzysz nowy kontener obiektów Blob podczas tworzenia avere vFXT dla klastra platformy Azure, szablon wdrożenia automatycznie konfiguruje kontener jako podstawowy filer. (Jest to również prawdą, jeśli używasz skryptu tworzenia, który jest dostępny na żądanie.) Nie trzeba później konfigurować głównego filera.
>
> Narzędzie do tworzenia klastra wykonuje następujące zadania konfiguracyjne:
>
> * Tworzy nowy kontener obiektu Blob na podanym koncie magazynu
> * Definiuje kontener jako podstawowy filer
> * Tworzy połączenie obszaru nazw do kontenera
> * Tworzy punkt końcowy usługi magazynu wewnątrz sieci wirtualnej klastra

Aby dodać magazyn obiektów Blob po utworzeniu klastra, wykonaj następujące kroki.

1. Utwórz ogólne konto magazynu v2 z tymi ustawieniami:

   * **Subskrypcja** — taka sama jak w klastrze vFXT
   * **Grupa zasobów** — taka sama jak grupa klastra vFXT (opcjonalnie)
   * **Lokalizacja** — taka sama jak w klastrze vFXT
   * **Wydajność** — standardowa (magazyn w wersji Premium nie jest nieobsługiwał)
   * **Rodzaj konta** — uniwersalny V2 (StorageV2)
   * **Replikacja** — magazyn dostępny lokalnie nadmiarowy (LRS)
   * **Warstwa dostępu** — gorąca
   * **Wymagany bezpieczny transfer** - wyłącz tę opcję (wartość nienawisowa)
   * **Sieci wirtualne** — nie jest wymagane

   Możesz użyć witryny Azure portal lub kliknij przycisk "Wdrażanie na platformie Azure" poniżej.

   [![, aby utworzyć konto magazynu](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Po utworzeniu konta przejdź do strony konta magazynu.

   ![Nowe konto magazynu w witrynie Azure portal](media/avere-vfxt-new-storage-acct.png)

1. Utwórz nowy kontener obiektów blob: kliknij pozycję **Kontenery** na stronie przeglądu, a następnie kliknij przycisk **+Kontener**. Użyj dowolnej nazwy kontenera i upewnij się, że dostęp jest ustawiony na **Prywatny**.

   ![Strona obiektów blob magazynu z zakreślonym przyciskiem +container i nowym kontenerem tworzonym na stronie podręcznej](media/avere-vfxt-new-blob.png)

1. Pobierz klucz konta usługi Azure Storage, klikając **przycisk Klucze programu Access** w obszarze **Ustawienia**. Skopiuj jeden z dostarczonych kluczy.

   ![Interfejs gui portalu platformy Azure do kopiowania klucza](media/avere-vfxt-copy-storage-key.png)

1. Otwórz Panel sterowania Avere dla swojego klastra. Kliknij **pozycję Ustawienia**, a następnie otwórz pozycję Poświadczenia chmury **klastra** > **Cloud Credentials** w lewym okienku nawigacji. Na stronie Poświadczenia w chmurze kliknij pozycję **Dodaj poświadczenia**.

   ![Kliknij przycisk Dodaj poświadczenia na stronie konfiguracji Poświadczenia w chmurze](media/avere-vfxt-new-credential-button.png)

1. Wypełnij następujące informacje, aby utworzyć poświadczenia dla filera z rdzeniem chmury:

   | Pole | Wartość |
   | --- | --- |
   | Nazwa poświadczenia | dowolna nazwa opisowa |
   | Typ usługi | (wybierz klucz dostępu usługi Azure Storage) |
   | Dzierżawa | nazwa konta magazynu |
   | Subskrypcja | subscription ID |
   | Klucz dostępu do magazynu | Klucz konta usługi Azure Storage (skopiowany w poprzednim kroku) |

   Kliknij **przycisk Prześlij**.

   ![Ukończony formularz poświadczeń w chmurze w Panelu sterowania Avere](media/avere-vfxt-new-credential-submit.png)

1. Następnie utwórz podstawowy filer. Po lewej stronie Panelu sterowania Avere kliknij pozycję **Core Filer** >  **Manage Core Filers**.

1. Kliknij przycisk **Utwórz** na stronie **Ustawienia podstawowych filerów.**

1. Wypełnij kreatora:

   * Wybierz typ filera **Chmura**.
   * Nazwij nowy podstawowy filer i kliknij przycisk **Dalej**.
   * Zaakceptuj domyślną zasadę pamięci podręcznej i przejdź do trzeciej strony.
   * W **obszarze Typ usługi**wybierz pozycję Magazyn platformy **Azure**.
   * Wybierz poświadczenia utworzone wcześniej.
   * Ustawianie **zawartości zasobnika** na **pustą**
   * Zmień **weryfikację certyfikatu** na **Wyłączona**
   * Zmień **tryb kompresji** na **Brak**
   * Kliknij przycisk **alej**.
   * Na czwartej stronie wpisz nazwę kontenera w nazwach **zasobnika** jako *storage_account_name*/*container_name*.
   * Opcjonalnie ustaw **typ szyfrowania** na **Brak**.  Usługa Azure Storage jest domyślnie szyfrowana.
   * Kliknij **pozycję Dodaj plik .**

   Aby uzyskać bardziej szczegółowe informacje, zobacz [Dodawanie nowego filera z rdzeniem chmury](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) w przewodniku konfiguracji klastra Avere.

Strona zostanie odświeżona lub można odświeżyć stronę, aby wyświetlić nowy podstawowy filer.

Następnie należy [utworzyć skrzyżowanie](#create-a-junction).

## <a name="create-a-junction"></a>Tworzenie skrzyżowania

Skrzyżowanie to ścieżka utworzona dla klientów. Klienci montują ścieżkę i docierają do wybranego miejsca docelowego.

Na przykład można `/vfxt/files` utworzyć do mapowania do `/vol0/data` eksportu plików `/project/resources` podstawowych NetApp i podkatalogu.

Więcej informacji na temat połączeń można znaleźć w [sekcji obszarów nazw w przewodniku konfiguracji klastra Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Wykonaj następujące kroki w interfejsie Panelu sterowania Avere:

* W lewym górnym rogu kliknij pozycję Obszar nazw **serwera VServer.** > **Namespace**
* Podaj ścieżkę obszaru nazw rozpoczynającą ``/vfxt/data``się od / (ukośnik do przodu), na podobny .
* Wybierz podstawowy filer.
* Wybierz podstawowy eksport filera.
* Kliknij przycisk **alej**.

  ![Zrzut ekranu przedstawiający stronę "Dodaj nowe skrzyżowanie" z polami ukończonymi dla złącza, głównego filera i eksportu](media/avere-vfxt-add-junction.png)

Skrzyżowanie pojawi się po kilku sekundach. W razie potrzeby utwórz dodatkowe skrzyżowania.

Po utworzeniu skrzyżowania klienci używają ścieżki obszaru nazw, aby uzyskać dostęp do plików z systemu magazynu.

## <a name="next-steps"></a>Następne kroki

* [Instalowanie klastra Avere vFXT](avere-vfxt-mount-clients.md)
* Dowiedz się skuteczne sposoby [przenoszenia danych do nowego kontenera obiektów Blob](avere-vfxt-data-ingest.md)
