---
title: Instalowanie pamięci podręcznej HPC platformy Azure
description: Jak połączyć klientów z usługą Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657363"
---
# <a name="mount-the-azure-hpc-cache"></a>Instalowanie pamięci podręcznej HPC platformy Azure

Po utworzeniu pamięci podręcznej klienci NFS `mount` mogą uzyskać do niej dostęp za pomocą prostego polecenia. Polecenie łączy ścieżkę docelową określonego magazynu w pamięci podręcznej HPC platformy Azure z katalogiem lokalnym na komputerze klienckim.

Polecenie mount składa się z następujących elementów:

* Jeden z adresów instalacji pamięci podręcznej (wymieniony na stronie przeglądu pamięci podręcznej)
* Ścieżka wirtualnego obszaru nazw ustawiona podczas tworzenia obiektu docelowego magazynu
* Ścieżka lokalna do użycia na kliencie
* Parametry poleceń, które optymalizują powodzenie tego rodzaju instalacji NFS

Strona **Instrukcje instalacji** pamięci podręcznej zbiera informacje i zalecane opcje dla Ciebie i tworzy polecenie instalacji prototypu, które można skopiować. Przeczytaj [artykuł Użyj narzędzia instrukcji montażu, aby](#use-the-mount-instructions-utility) uzyskać szczegółowe informacje.

## <a name="prepare-clients"></a>Przygotowywanie klientów

Upewnij się, że klienci są w stanie zainstalować pamięć podręczną HPC platformy Azure, postępuje zgodnie z wytycznymi w tej sekcji.

### <a name="provide-network-access"></a>Zapewnienie dostępu do sieci

Maszyny klienckie muszą mieć dostęp sieciowy do sieci wirtualnej i podsieci prywatnej pamięci podręcznej.

Na przykład utwórz maszyny wirtualne klienta w tej samej sieci wirtualnej lub użyj punktu końcowego, bramy lub innego rozwiązania w sieci wirtualnej, aby uzyskać dostęp z zewnątrz. (Należy pamiętać, że nic innego niż sama pamięć podręczna powinna być hostowana wewnątrz podsieci pamięci podręcznej).

### <a name="install-utilities"></a>Instalowanie narzędzi

Zainstaluj odpowiednie oprogramowanie narzędziowe systemu Linux do obsługi polecenia do montażu NFS:

* Dla Red Hat Enterprise Linux lub SuSE:`sudo yum install -y nfs-utils`
* Dla Ubuntu lub Debiana:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Tworzenie ścieżki lokalnej

Utwórz ścieżkę katalogu lokalnego na każdym kliencie, aby połączyć się z pamięcią podręczną. Utwórz ścieżkę dla każdego miejsca docelowego magazynu, który chcesz zainstalować.

Przykład: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Użyj narzędzia instrukcji montażu

Otwórz stronę **instrukcje instalacji** z sekcji **Konfigurowanie** widoku pamięci podręcznej w witrynie Azure portal.

![zrzut ekranu przedstawiający wystąpienie pamięci podręcznej HPC platformy Azure w portalu z załadowaną stroną Instrukcje konfigurowania > montażu](media/mount-instructions.png)

Strona polecenia instalacji zawiera informacje o procesie instalacji klienta i wymagania wstępne, a także pola, których można użyć do utworzenia polecenia instalacji do kopiowania.

Aby korzystać z tej strony, wykonaj tę procedurę:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Przejrzyj wymagania wstępne klienta i zainstaluj narzędzia potrzebne do `mount` użycia polecenia NFS, jak opisano powyżej w [aplikacji Przygotowywanie klientów](#prepare-clients).

1. Krok pierwszy **montaż systemu plików**<!-- label will change --> podaje przykładowe polecenie do tworzenia ścieżki lokalnej na kliencie. Jest to ścieżka, która będzie używana przez klienta do uzyskiwania dostępu do zawartości z pamięci podręcznej HPC platformy Azure.

   Zanotuj nazwę ścieżki, dzięki czemu można ją w razie potrzeby zmodyfikować w poleceniu.

1. W kroku drugim wybierz jeden z dostępnych adresów IP. Wszystkie [punkty instalacji klienta](#find-mount-command-components) pamięci podręcznej są wymienione tutaj. Upewnij się, że masz system, aby zrównoważyć obciążenie między wszystkimi adresami IP.

1. Pole w kroku trzecim automatycznie wypełnia się poleceniem montowania prototypu. Kliknij symbol kopiowania po prawej stronie pola, aby automatycznie skopiować go do schowka.

   > [!NOTE]
   > Przed użyciem należy zaznaczyć polecenie kopiowania. Może być konieczne dostosowanie ścieżki instalacji klienta i ścieżki docelowego wirtualnego obszaru nazw magazynu, które nie są jeszcze wybierane w tym interfejsie. Należy również zaktualizować opcje polecenia instalacji, aby odzwierciedlić [zalecane opcje](#mount-command-options) poniżej. Przeczytaj [opis składni polecenia instalacji, aby](#understand-mount-command-syntax) uzyskać pomoc.

1. Użyj skopiowanego polecenia instalacji (z edycjami, jeśli to konieczne) na komputerze klienckim, aby połączyć je z obiektem docelowym magazynu w pamięci podręcznej HPC platformy Azure. Można wydać polecenie bezpośrednio z wiersza polecenia klienta lub dołączyć polecenie mount do skryptu lub szablonu konfiguracji klienta.

## <a name="understand-mount-command-syntax"></a>Opis składni polecenia montowania

Polecenie mount ma następujący formularz:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*options*}

Przykład:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
root@test-client:/tmp#
```

Po pomyślnym powiódł się to polecenie, zawartość eksportu ``hpccache`` magazynu będzie widoczna w katalogu na kliencie.

### <a name="mount-command-options"></a>Opcje polecenia Montuj

W przypadku niezawodnego montażu klienta należy przekazać te ustawienia i argumenty w poleceniu instalacji:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Zalecane ustawienia polecenia montowania | |
--- | ---
``hard`` | Miękkie instalacje do pamięci podręcznej HPC usługi Azure są skojarzone z awariami aplikacji i możliwością utraty danych.
``proto=tcp`` | Ta opcja obsługuje odpowiednią obsługę błędów sieci NFS.
``mountproto=tcp`` | Ta opcja obsługuje odpowiednią obsługę błędów sieciowych dla operacji instalacji.
``retry=<value>`` | Ustaw, ``retry=30`` aby uniknąć przejściowych błędów instalacji. (W instalacjach pierwszego planu zalecana jest inna wartość).

### <a name="find-mount-command-components"></a>Znajdowanie składników polecenia mount

Jeśli chcesz utworzyć polecenie instalacji bez użycia strony **Instrukcje instalacji,** możesz znaleźć adresy instalacji na stronie **Przegląd** pamięci podręcznej i ścieżki wirtualnego obszaru nazw na stronie **Obiekty docelowe magazynu.**

![zrzut ekranu przedstawiający stronę Przegląd wystąpienia usługi Azure HPC Cache z polem wyróżnienia wokół listy adresów instalacji w prawym dolnym przycisku](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Adresy instalacji pamięci podręcznej odpowiadają interfejsom sieciowym wewnątrz podsieci pamięci podręcznej. W grupie zasobów te karty sieciowe są `-cluster-nic-` wyświetlane z nazwami kończącymi się i liczbą. Nie należy zmieniać ani usuwać tych interfejsów, ponieważ pamięć podręczna stanie się niedostępna.

Ścieżki wirtualnego obszaru nazw są wyświetlane na stronie **Obiekty docelowe magazynu.** Kliknij nazwę obiektu docelowego magazynu, aby wyświetlić jej szczegóły, w tym skojarzone z nią zagregowane ścieżki obszaru nazw.

![zrzut ekranu panelu docelowego Magazyn pamięci podręcznej z polem podświetlenia wokół wpisu w kolumnie Ścieżka tabeli](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Następne kroki

* Aby przenieść dane do obiektów docelowych magazynu pamięci podręcznej, przeczytaj Artykuł [Wypełnianie nowego magazynu obiektów Blob platformy Azure](hpc-cache-ingest.md).
