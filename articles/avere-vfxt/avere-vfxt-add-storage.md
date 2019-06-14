---
title: Konfigurowanie magazynu vFXT Avere - Azure
description: Jak dodać system magazynu zaplecza do usługi vFXT Avere dla platformy Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 6d35d5cdeafb80a36f910d71393802a3affb4df8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515933"
---
# <a name="configure-storage"></a>Konfigurowanie magazynu

W tym kroku konfiguruje system magazynu zaplecza vFXT klastra.

> [!TIP]
> Jeśli utworzono nowy kontener obiektów Blob platformy Azure wraz z klastrem vFXT Avere tego kontenera jest już skonfigurowany do użycia i nie trzeba dodać magazyn.

Wykonaj te instrukcje, jeśli nie utworzono nowy kontener obiektów Blob na potrzeby klastra lub jeśli chcesz dodać dodatkowego sprzętu ani system magazynu w chmurze.

Istnieją dwa główne zadania:

1. [Utwórz filtr core](#create-a-core-filer), klaster vFXT łączy się z istniejącego systemu magazynu lub konto usługi Azure Storage.

1. [Utwórz połączenie przestrzeni nazw](#create-a-junction), która określa ścieżkę, która zainstaluje klientów.

Te kroki odnoszą się Avere Panelu sterowania. Odczyt [dostęp do klastra vFXT](avere-vfxt-cluster-gui.md) dowiesz się, jak z niego korzystać.

## <a name="create-a-core-filer"></a>Utwórz filtr core

"Core filtr" to termin vFXT systemu magazynu zaplecza. Magazyn może być urządzenie NAS sprzętu, takich jak NetApp lub Isilon lub może być magazynem obiektów w chmurze. Można znaleźć więcej informacji o filtrach core [w Avere klastra przewodnik ustawienia](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Aby dodać filtr core, wybierz jedną z dwa główne rodzaje filtrach core:

  * [NAS podstawowe filtr](#nas-core-filer) — w tym artykule opisano sposób dodawania filtr core NAS 
  * [Filtr core chmury w usłudze Azure magazynu](#azure-storage-cloud-core-filer) — w tym artykule opisano sposób dodawania konta usługi Azure Storage jako filtr core chmury

### <a name="nas-core-filer"></a>Filtr core NAS

Filtr core NAS może być NetApp lokalnych Isilon i/lub punkt końcowy NAS w chmurze. System magazynu musi mieć niezawodnego połączenia o dużej szybkości do klastra vFXT Avere — na przykład 1GBps połączenia ExpressRoute (nie sieć VPN) — i wywozu NAS używany musi udzielić dostępu do katalogu głównego klastra.

Poniższe kroki należy dodać filtr core NAS:

1. W Panelu sterowania Avere kliknij **ustawienia** kartę u góry.

1. Kliknij przycisk **filtr Core** > **wygląda podstawowe zarządzanie** po lewej stronie.

1. Kliknij pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający Dodaj nowe podstawowe filtr strony z kursor nad przycisk Utwórz](media/avere-vfxt-add-core-filer-start.png)

1. Podaj wymagane informacje w Kreatorze: 

   * Nazwij filtr sieci podstawowej.
   * Podaj w pełni kwalifikowaną nazwę domeny (FQDN), jeśli jest dostępny. W przeciwnym razie Podaj adres IP lub nazwa hosta, który jest rozpoznawany jako filtr usługi podstawowej.
   * Wybierz filtr klasy z listy. Jeśli nie wiesz, wybierz opcję **innych**.

     ![Zrzut ekranu przedstawiający stronę filtr core nowe Dodaj o nazwie filtr podstawowych i jego w pełni kwalifikowanej nazwy domeny](media/avere-vfxt-add-core-filer.png)
  
   * Kliknij przycisk **dalej** i wybierz zasady pamięci podręcznej. 
   * Kliknij przycisk **Dodaj filtr**.
   * Aby uzyskać szczegółowe informacje, zobacz [Dodawanie nowych NAS podstawowe filtr](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) w Avere klastra przewodnik ustawienia.

Następnie przejdź do [utworzyć Rozgałęzienie](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Filtr core chmury w usłudze Azure magazynu

Aby użyć usługi Azure Blob storage jako magazynu zaplecza w klastrze vFXT, należy pustego kontenera do dodania jako filtr core.

> [!TIP] 
> Jeśli zdecydujesz się utworzyć kontenera obiektów blob w tym samym czasie, możesz utworzyć klaster vFXT Avere, Szablon wdrożenia lub skrypt tworzy kontener magazynu, definiuje ją jako filtr podstawowych i tworzy połączenie przestrzeni nazw jako część tworzenia klastra vFXT. Ten szablon tworzy również punkt końcowy usługi magazynu w sieci wirtualnej klastra. 

Dodawanie magazynu obiektów Blob do klastra wymaga tych zadań:

* Tworzenie konta magazynu (krok 1 poniżej)
* Tworzenie pustego kontenera obiektów Blob (kroki 2 – 3)
* Dodaj klucz dostępu do magazynu jako poświadczenia w chmurze dla klastra vFXT (kroki od 4 do 6)
* Dodaj kontener obiektów Blob jako filtr podstawowej dla klastra vFXT (kroki 7 – 9)
* Utwórz połączenie przestrzeni nazw, używanego przez klientów należy zainstalować filtr core ([utworzyć Rozgałęzienie](#create-a-junction), tym samym do magazynowania zarówno w sprzęt, jak i w chmurze)

Aby dodać magazyn obiektów Blob, po utworzeniu klastra, wykonaj następujące kroki. 

1. Utwórz konto magazynu ogólnego przeznaczenia w wersji 2 przy użyciu tych ustawień:

   * **Subskrypcja** — jest to taka sama jak vFXT klastra
   * **Grupa zasobów** — jest to taka sama jak Grupa klastra vFXT (opcjonalnie)
   * **Lokalizacja** — jest to taka sama jak vFXT klastra
   * **Wydajność** — standardowe (Premium storage jest nieobsługiwana)
   * **Rodzaj konta** -General-purpose V2 (StorageV2)
   * **Replikacja** — magazyn lokalnie nadmiarowy (LRS)
   * **Warstwa dostępu** — gorąca
   * **Wymagany bezpieczny transfer** -Wyłącz tę opcję (innych niż domyślne wartości)
   * **Sieci wirtualne** — nie jest wymagane

   Możesz użyć witryny Azure portal, lub kliknij poniższy przycisk "Wdróż na platformie Azure".

   [![przycisk, aby utworzyć konto magazynu](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Po utworzeniu konta, przejdź na stronę konta magazynu.

   ![Nowe konto magazynu w witrynie Azure portal](media/avere-vfxt-new-storage-acct.png)

1. Utwórz kontener obiektów blob, klikając **obiektów blob** na stronie Przegląd, a następnie kliknij przycisk **+ kontener**. Użyć dowolnej nazwy kontenera i upewnij się, że dostęp jest ustawiona na **prywatnej**.

   ![Magazyn obiektów blob strony z nie istniejących kontenerów](media/avere-vfxt-blob-no-container.png)

1. Pobierz klucz konta usługi Azure Storage, klikając **klucze dostępu** w obszarze **ustawienia**:

   ![Witryny Azure portal graficzny interfejs użytkownika umożliwiający skopiowanie klucza](media/avere-vfxt-copy-storage-key.png) 

1. Otwórz Panel sterowania Avere dla klastra. Kliknij przycisk **ustawienia**, a następnie otwórz **klastra** > **poświadczenia chmury** w okienku nawigacji po lewej stronie. Na stronie poświadczenia chmury kliknij **Dodaj poświadczenie**.

   ![Kliknij przycisk Dodaj poświadczenia na stronie konfiguracji poświadczenia chmury](media/avere-vfxt-new-credential-button.png)

1. Wypełnij następujące informacje, aby utworzyć poświadczenie dla chmury filtr core: 

   | Pole | Wartość |
   | --- | --- |
   | Nazwa poświadczenia | Dowolna nazwa opisowa |
   | Typ usługi | (Wybierz klucz dostępu do magazynu Azure) |
   | Dzierżawa | Nazwa konta magazynu |
   | Subskrypcja | Identyfikator subskrypcji |
   | Klucz dostępu do magazynu | Klucz konta usługi Azure storage (skopiowany w poprzednim kroku) | 

   Kliknij przycisk **Submit** (Prześlij).

   ![Ukończono formularza poświadczenia chmury w Panelu sterowania Avere](media/avere-vfxt-new-credential-submit.png)

1. Następnie należy utworzyć filtr core. W lewej części panelu sterowania Avere, kliknij przycisk **filtr Core** >  **wygląda podstawowe zarządzanie**. 

1. Kliknij przycisk **Utwórz** znajdujący się na **wygląda podstawowe zarządzanie** strony ustawień.

1. Wypełnij kreatora:

   * Wybierz typ filtr **chmury**. 
   * Nazwij nowy filtr core, a następnie kliknij przycisk **dalej**.
   * Zaakceptuj domyślne zasady pamięci podręcznej, a następnie przejdź do strony trzeciej.
   * W **typ usługi**, wybierz **usługi Azure storage**. 
   * Wybierz utworzone wcześniej poświadczenie.
   * Ustaw **zasobnika zawartość** do **pusty**
   * Zmiana **certyfikatu weryfikacji** do **wyłączone**
   * Zmiana **tryb kompresji** do **None**  
   * Kliknij przycisk **Dalej**.
   * Na czwartej stronie wprowadź nazwę kontenera w **Nazwa zasobnika** jako *storage_account_name*/*container_name*.
   * Opcjonalnie można ustawić **typ szyfrowania** do **Brak**.  Usługa Azure Storage jest domyślne szyfrowanie przekazywanego materiału.
   * Kliknij przycisk **Dodaj filtr**.

   Aby uzyskać szczegółowe informacje, przeczytaj [Dodawanie nowych filtr core chmury](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) w przewodniku Avere konfiguracji klastra. 

Strona zostanie odświeżona lub możesz odświeżyć stronę, aby wyświetlić swoje nowy filtr core.

Następnie należy [utworzyć Rozgałęzienie](#create-a-junction).

## <a name="create-a-junction"></a>Utwórz połączenie

Połączenie jest ścieżką, które tworzysz dla klientów. Klienci ścieżkę instalacji i pojawić się w wybranej lokalizacji docelowej.

Na przykład można utworzyć `/avere/files` do mapowania na Twoje filtr core NetApp `/vol0/data` eksportu i `/project/resources` podkatalogu.

Więcej informacji o punktach transferu znajdują się w [przestrzeni nazw części przewodnika konfiguracji klastra Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Wykonaj następujące kroki w interfejsie ustawienia Panelu sterowania Avere:

* Kliknij przycisk **VServer** > **Namespace** w lewym górnym rogu.
* Podaj obszar nazw ścieżki rozpoczynającej się od, / (ukośnik), takiej jak ``/avere/data``.
* Wybierz filtr sieci podstawowej.
* Wybierz opcję Eksportuj filtr core.
* Kliknij przycisk **Dalej**.

  ![Zrzut ekranu przedstawiający stronę "Dodaj nowe połączenie" z polami Zakończono połączenie, filtr podstawowych i eksportowanie](media/avere-vfxt-add-junction.png)

Połączenie zostanie wyświetlony po kilku sekundach. Utwórz dodatkowe punktach transferu, zgodnie z potrzebami.

Po utworzeniu połączenia klientów można [instalacji klastra vFXT Avere](avere-vfxt-mount-clients.md) dostęp do systemu plików.
