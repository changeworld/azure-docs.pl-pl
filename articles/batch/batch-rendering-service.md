---
title: Azure Batch Rendering — renderowanie w skali chmury | Microsoft Docs
description: Renderuj zadania na maszynach wirtualnych platformy Azure bezpośrednio z programu Maya z opłatami za użycie.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: danlep
ms.openlocfilehash: 954a67ed126b505c9ba0da81b3ace0d25e840adb
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128592"
---
# <a name="get-started-with-batch-rendering"></a>Wprowadzenie do usługi Batch Rendering 

Usługa Azure Batch Rendering oferuje możliwości renderowania w skali chmury z opłatami za użycie. Usługa Batch Rendering obsługuje planowanie i kolejkowanie zadań, zarządzanie niepowodzeniami i ponawianiem prób oraz automatyczne skalowanie zadań renderowania. Usługa Batch Rendering obsługuje aplikacje służące do renderowania, w tym programy [Autodesk Maya](https://www.autodesk.com/products/maya/overview), [3ds Max](https://www.autodesk.com/products/3ds-max/overview), [Arnold](https://www.autodesk.com/products/arnold/overview) i [V-Ray](https://www.chaosgroup.com/vray/maya). Wtyczka usługi Batch dla programu Maya 2017 ułatwia uruchamianie zadań renderowania na platformie Azure bezpośrednio z Twojego komputera.

Za pomocą programów Maya i 3ds Max możesz uruchamiać zadania przy użyciu aplikacji klasycznej [BatchLabs](https://github.com/Azure/BatchLabs) lub [szablonów interfejsu wiersza polecenia usługi Batch](batch-cli-templates.md). Przy użyciu interfejsu wiersza polecenia usługi Azure Batch możesz uruchomić zadania usługi Batch bez pisania kodu. Zamiast tego możesz użyć plików szablonów do tworzenia pul, zadań i zadań podrzędnych usługi Batch. Więcej informacji można znaleźć w temacie [Use Azure Batch CLI Templates and File Transfer](batch-cli-templates.md) (Korzystanie z szablonów interfejsu wiersza polecenia usługi Azure Batch i transferu plików).


## <a name="supported-applications"></a>Obsługiwane aplikacje

Usługa Batch Rendering obecnie obsługuje następujące aplikacje:

W węzłach renderowania CentOS 7:
- Autodesk Maya I/O 2017 Update 5 (wersja 201708032230)
- Autodesk Maya I/O 2018 Update 2 wersja 201711281015
- Autodesk Arnold for Maya 2017 (wersja Arnold 5.0.1.1) MtoA-2.0.1.1-2017
- Autodesk Arnold for Maya 2018 (wersja Arnold 5.0.1.4) MtoA-2.1.0.3-2018
- Chaos Group V-Ray for Maya 2017 (wersja 3.60.04) 
- Chaos Group V-Ray for Maya 2018 (wersja 3.60.04) 
- Blender (2.68)

W węzłach renderowania Windows Server 2016:
- Autodesk Maya I/O 2017 Update 5 (wersja 17.4.5459) 
- Autodesk Maya I/O 2018 Update 2 (wersja 18.2.0.6476) 
- Autodesk 3ds Max I/O 2018 Update 4 (wersja 20.4.0.4254) 
- Autodesk Arnold for Maya (wersja Arnold 5.0.1.1) MtoA-2.0.1.1-2017
- Autodesk Arnold for Maya (wersja Arnold 5.0.1.4) MtoA-2.0.2.3-2018
- Autodesk Arnold for 3ds Max (wersja Arnold 5.0.2.4 )(wersja 1.2.926) 
- Chaos Group V-Ray for Maya (wersja 3.52.03) 
- Chaos Group V-Ray for 3ds Max (wersja 3.60.02)
- Blender (2.79)


## <a name="prerequisites"></a>Wymagania wstępne

Do korzystania z usługi Batch Rendering potrzebne są następujące elementy:

- [Konto platformy Azure](https://azure.microsoft.com/free/).
- **Konto usługi Azure Batch.** Aby uzyskać wskazówki dotyczące tworzenia konta usługi Batch w witrynie Azure Portal, zobacz [Create a Batch account with the Azure portal](batch-account-create-portal.md) (Tworzenie konta usługi Batch w witrynie Azure Portal).
- **Konto usługi Azure Storage.** Zasoby używane w ramach zadania renderowania są zwykle przechowywane w usłudze Azure Storage. Konto magazynu możesz utworzyć automatycznie podczas konfigurowania konta usługi Batch. Możesz także użyć istniejącego konta magazynu. Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](batch-api-basics.md#azure-storage-account).
- **Zmienne środowiskowe.** Jeśli rozwiązanie modyfikuje zmienne środowiskowe, upewnij się, że wartości `AZ_BATCH_ACCOUNT_URL` i `AZ_BATCH_SOFTWARE_ENTITLEMENT_TOKEN` pozostaną nienaruszone i dostępne, gdy wywoływana jest dowolna z wyżej wymienionych licencjonowanych aplikacji. W przeciwnym razie użytkownik prawdopodobnie napotka problemy dotyczące aktywacji oprogramowania.
- **BatchLabs** (opcjonalnie). [BatchLabs](https://azure.github.io/BatchLabs) to bezpłatne, bogate w funkcje, autonomiczne narzędzie klienta pomagające tworzyć, debugować i monitorować aplikacje usługi Azure Batch. Chociaż użycie usługi Rendering nie jest wymagane, jest to przydatna opcja w przypadku wdrażania i debugowania rozwiązań usługi Batch.

Aby użyć wtyczki usługi Batch dla programu Maya, wymagane są następujące elementy:

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview).
- Obsługiwane programy renderujące, takie jak Arnold for Maya lub V-Ray for Maya.

## <a name="basic-batch-concepts"></a>Podstawowe pojęcia usługi Batch

Przed rozpoczęciem korzystania z usługi Batch Rendering należy zaznajomić się z kilkoma podstawowymi pojęciami usługi Batch, w tym węzłami obliczeniowymi, pulami i zadaniami. Aby uzyskać więcej ogólnych informacji o usłudze Azure Batch, zobacz [Run intrinsically parallel workloads with Batch](batch-technical-overview.md) (Uruchamianie wewnętrznie równoległych obciążeń przy użyciu usługi Batch).

### <a name="pools"></a>Pule

Usługa Batch to usługa platformy do obsługi zadań związanych z intensywnymi obliczeniami, np. renderowania, w oparciu o **pulę** **węzłów obliczeniowych**. Każdy węzeł obliczeniowy w puli jest maszyną wirtualną platformy Azure z systemem Linux lub Windows. 

Aby uzyskać więcej informacji o pulach i węzłach obliczeniowych usługi Batch, zobacz sekcje [Pula](batch-api-basics.md#pool) i [Węzeł obliczeniowy](batch-api-basics.md#compute-node) w temacie [Tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).

### <a name="jobs"></a>Zadania

**Zadanie** usługi Batch to zbiór zadań podrzędnych uruchamianych w węzłach obliczeniowych w puli. Po przesłaniu zadania renderowania usługa Batch dzieli zadanie na wiele zadań podrzędnych i dystrybuuje je w celu uruchomienia do węzłów obliczeniowych w puli.

Za pomocą witryny [Azure Portal](https://ms.portal.azure.com/) możesz monitorować zadania i diagnozować zadania podrzędne zakończone niepowodzeniem przez pobieranie dzienników aplikacji oraz zdalne łączenie się z poszczególnymi maszynami wirtualnymi przy użyciu protokołu RDP lub SSH. Możesz również zarządzać, monitorować i debugować za pomocą [narzędzia BatchLabs](https://azure.github.io/BatchLabs).

Aby uzyskać więcej informacji o zadaniach usługi Batch, zobacz sekcję [Zadanie](batch-api-basics.md#job) w temacie [Tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).

## <a name="options-for-provisioning-required-applications"></a>Opcje aprowizacji wymaganych aplikacji

Do renderowania zadania może być potrzebne wiele aplikacji, na przykład połączenie aplikacji Maya i Arnold lub 3ds Max i V Ray, oraz inne wtyczki firm trzecich, jeśli jest to wymagane. Ponadto niektórzy klienci mogą wymagać określonych wersji tych aplikacji. W ten sposób dostępnych jest kilka metod aprowizacji wymaganych aplikacji oraz oprogramowania:

### <a name="pre-configured-vm-images"></a>Wstępnie skonfigurowane obrazy maszyn wirtualnych

Platforma Azure udostępnia obrazy systemu Windows i Linux dla każdej oddzielnej wersji wstępnie zainstalowanych i gotowych do użycia aplikacji Maya, 3ds Max, Arnold i V-Ray. Możesz wybrać te obrazy w witrynie [Azure Portal](https://portal.azure.com), wtyczce Maya lub aplikacji [BatchLabs](https://azure.github.io/BatchLabs) podczas tworzenia puli.

W witrynie Azure Portal i w aplikacji BatchLabs możesz w następujący sposób zainstalować jeden z obrazów maszyn wirtualnych ze wstępnie zainstalowanymi aplikacjami: w sekcji Pule swojego konta usługi Batch wybierz pozycję **Nowy**, a następnie w obszarze **Dodaj pulę** wybierz pozycję **Grafika i renderowanie (Linux/Windows)** na liście rozwijanej **Typ obrazu**:

![Wybieranie typu obrazu dla konta usługi Batch](./media/batch-rendering-service/add-pool.png)

Przewiń w dół i w obszarze **Licencjonowanie grafiki i renderowania** kliknij przycisk **Wybierz oprogramowanie i cennik**. Wybierz przynajmniej jedną licencję na oprogramowanie:

![Wybieranie licencji grafiki i renderowania dla puli](./media/batch-rendering-service/graphics-licensing.png)

Udostępnione konkretne wersje licencji odpowiadają wersjom podanym w powyższej sekcji „Obsługiwane aplikacje”.

### <a name="custom-images"></a>Obrazy niestandardowe

Usługa Azure Batch umożliwia podanie własnego niestandardowego obrazu. Przy użyciu tej opcji możesz skonfigurować swoją maszynę wirtualną z odpowiednimi aplikacjami w określonych wersjach, których potrzebujesz. Aby uzyskać więcej informacji, zobacz [Use a custom image to create a pool of virtual machines (Używanie obrazu niestandardowego do tworzenia puli maszyn wirtualnych)](https://docs.microsoft.com/azure/batch/batch-custom-images). Należy pamiętać, że firmy Autodesk i Chaos Group zmodyfikowały aplikacje Arnold i V-Ray, odpowiednio, na potrzeby weryfikacji własnej usługi licencjonowania. Musisz się upewnić, że masz wersje tych aplikacji z tą pomocą techniczną, w przeciwnym razie licencjonowanie na podstawie opłaty za użycie nie będzie działać. Ta weryfikacja licencji nie jest wymagana dla aplikacji Maya lub 3ds Max, ponieważ bieżące opublikowane wersje nie wymagają serwera licencji podczas uruchamiania bezobsługowego (w trybie wsadowym/wiersza polecenia). Jeśli nie masz pewności, jak korzystać z tej opcji, skontaktuj się z działem pomocy technicznej platformy Azure.

## <a name="options-for-submitting-a-render-job"></a>Opcje przesyłania zadania renderowania

W zależności od używanej aplikacji 3D istnieją różne opcje przesyłania zadań renderowania:

### <a name="maya"></a>Maya

W przypadku aplikacji Maya możesz użyć:

- [Wtyczki usługi Batch dla aplikacji Maya](https://docs.microsoft.com/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- Aplikacji klasycznej [BatchLabs](https://azure.github.io/BatchLabs)
- [Interfejsu wiersza polecenia szablonów usługi Batch](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

W przypadku aplikacji 3ds Max możesz użyć:

- Aplikacji klasycznej [BatchLabs](https://azure.github.io/BatchLabs) (zobacz sekcję dotyczącą [danych aplikacji BatchLabs](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax), aby uzyskać wskazówki dotyczące używania szablonów aplikacji 3ds Max BatchLabs)
- [Interfejsu wiersza polecenia szablonów usługi Batch](batch-cli-templates.md)

Szablony 3ds Max BatchLabs umożliwiają renderowanie scen aplikacji VRay i Arnold przy użyciu usługi Batch Rendering. Istnieją dwie odmiany szablonu dla aplikacji VRay i Arnold: jeden dla standardowych scen i jeden dla bardziej złożonych scen, które wymagają pliku ścieżki aplikacji 3ds Max do zasobów i tekstur (plik mxp). Aby uzyskać więcej informacji o szablonach aplikacji 3ds Max BatchLabs, zobacz repozytorium [Dane aplikacji BatchLabs](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) w usłudze GitHub.

Ponadto możesz użyć [zestawu SDK języka Python usługi Batch](https://docs.microsoft.com/azure/batch/batch-python-tutorial), aby zintegrować usługę Rendering z istniejącym potokiem.


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Korzystanie z wtyczki usługi Batch dla programu Maya do przesyłania zadania renderowania

Za pomocą wtyczki usługi Batch dla programu Maya możesz przesłać zadanie do usługi Batch Rendering bezpośrednio z programu Maya. W następujących sekcjach opisano, jak można skonfigurować zadanie z poziomu wtyczki, a następnie je przesłać. 

### <a name="load-the-batch-plug-in-for-maya"></a>Ładowanie wtyczki usługi Batch dla aplikacji Maya

Wtyczka usługi Batch jest dostępna w usłudze [GitHub](https://github.com/Azure/azure-batch-maya/releases). Rozpakuj archiwum do wybranego katalogu. Wtyczkę możesz załadować bezpośrednio z katalogu *azure_batch_maya*.

Aby załadować wtyczkę w programie Maya:

1. Uruchom program Maya.
2. Wybierz pozycję **Window (Okno)** > **Settings/Preferences (Ustawienia/preferencje)** > **Plug-in Manager (Menedżer wtyczek)**.
3. Kliknij pozycję **Browse (Przeglądaj)**.
4. Wskaż plik *azure_batch_maya/plug-in/AzureBatch.py* i wybierz go.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>Uwierzytelnianie dostępu do kont usługi Batch i Storage

Aby użyć wtyczki, musisz przeprowadzić uwierzytelnianie przy użyciu kluczy kont usług Azure Batch i Azure Storage. Aby pobrać klucze konta:

1. Wyświetl wtyczkę w programie Maya i wybierz kartę **Konfiguracja**.
2. Przejdź do witryny [Azure Portal](https://portal.azure.com).
3. Wybierz opcję **Konta usługi Batch** z menu po lewej stronie. W razie potrzeby kliknij opcję **Więcej usług** i filtruj według kryterium _Batch_.
4. Znajdź żądane konto usługi Batch na liście.
5. Wybierz element menu **Klucze**, aby wyświetlić nazwę, adres URL i klucze dostępu konta:
    - Wklej adres URL konta usługi Batch do pola **Usługa** we wtyczce usługi Batch.
    - Wklej nazwę konta do pola **Konto usługi Batch**.
    - Wklej podstawowy klucz konta do pola **Klucz usługi Batch**.
7. Wybierz pozycję Konta usługi Storage z menu po lewej stronie. W razie potrzeby kliknij opcję **Więcej usług** i filtruj według kryterium _Storage_.
8. Znajdź żądane konto usługi Storage na liście.
9. Wybierz element menu **Klucze dostępu**, aby wyświetlić nazwę i klucze konta magazynu.
    - Wklej nazwę konta usługi Storage do pola **Konto usługi Storage** we wtyczce usługi Batch.
    - Wklej podstawowy klucz konta do pola **Klucz usługi Storage**.
10. Kliknij przycisk **Uwierzytelnij**, aby upewnić się, że wtyczka może uzyskać dostęp do obu kont.

Po pomyślnym uwierzytelnieniu wtyczka ustawia pole stanu na **Uwierzytelniono**: 

![Uwierzytelnianie kont usług Batch i Storage](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Konfigurowanie puli pod kątem zadania renderowania

Po uwierzytelnieniu kont usług Batch i Storage skonfiguruj pulę pod kątem zadania renderowania. Wtyczka zapisuje wybory między sesjami. Po ustawieniu preferowanej konfiguracji nie musisz jej modyfikować, chyba że ulegnie ona zmianie.

Poniższe sekcje przeprowadzą Cię przez opcje dostępne na karcie **Przesyłanie**:

#### <a name="specify-a-new-or-existing-pool"></a>Określanie nowej lub istniejącej puli

Aby określić pulę, w której chcesz uruchomić zadanie renderowania, wybierz kartę **Przesyłanie**. Ta karta oferuje opcje umożliwiające utworzenie puli lub wybranie istniejącej puli:

- **Automatyczne aprowizowanie puli dla tego zadania** (opcja domyślna). Gdy wybierzesz tę opcję, usługa Batch utworzy pulę wyłącznie dla bieżącego zadania, a następnie automatycznie usunie ją po zakończeniu zadania renderowania. Ta opcja sprawdza się najlepiej wtedy, gdy masz jedno zadanie renderowania do wykonania.
- **Ponowne użycie istniejącej puli trwałej**. Jeśli masz istniejącą pulę, która pozostaje bezczynna, możesz wybrać tę pulę do uruchomienia zadania renderowania poprzez wybranie jej z listy rozwijanej. Ponowne użycie istniejącej puli trwałej oszczędza czas wymagany do aprowizacji puli.  
- **Utworzenie nowej puli trwałej**. Wybranie tej opcji spowoduje utworzenie nowej puli na potrzeby uruchomienia zadania. Pula nie zostanie usunięta po zakończeniu zadania. Będzie ona dostępna do ponownego użycia na potrzeby przyszłych zadań. Wybierz tę opcję, gdy masz potrzebę ciągłego uruchamiania zadań renderowania. W przypadku kolejnych zadań możesz **ponownie użyć istniejącej puli trwałej**, aby skorzystać z puli trwałej utworzonej dla pierwszego zadania.

![Określanie puli, obrazu systemu operacyjnego, rozmiaru maszyny wirtualnej i licencji](./media/batch-rendering-service/submit.png)

Aby uzyskać więcej informacji o naliczaniu opłat dla maszyn wirtualnych platformy Azure, zobacz [Często zadawane pytania dotyczące cen dla systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) oraz [Często zadawane pytania dotyczące cen dla systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq).

#### <a name="specify-the-os-image-to-provision"></a>Określanie obrazu systemu operacyjnego do aprowizacji

Typ obrazu systemu operacyjnego do użycia, aby aprowizować węzły obliczeniowe w puli, możesz określić na karcie **Środowisko**. Usługa Batch obecnie obsługuje następujące opcje obrazów w przypadku zadań renderowania:

|System operacyjny  |Image (Obraz)  |
|---------|---------|
|Linux     |Batch CentOS |
|Windows     |Batch Windows |

#### <a name="choose-a-vm-size"></a>Wybieranie rozmiaru maszyny wirtualnej

Rozmiar maszyny wirtualnej możesz określić na karcie **Środowisko**. Aby uzyskać więcej informacji o dostępnych rozmiarach maszyn wirtualnych, zobacz [Linux VM sizes in Azure (Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure)](../virtual-machines/linux/sizes.md) oraz [Windows VM sizes in Azure (Rozmiary maszyn wirtualnych z systemem Windows na platformie Azure)](../virtual-machines/windows/sizes.md). 

![Określanie obrazu systemu operacyjnego i rozmiaru maszyny wirtualnej na karcie Środowisko](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Określanie opcji licencjonowania

Licencje do użycia możesz określić na karcie **Środowisko**. Dostępne są następujące opcje:

- **Maya** — włączona domyślnie.
- **Arnold** — włączona, jeśli jako aktywny aparat renderowania w programie Maya wykryto aparat Arnold.

 Jeśli chcesz renderować przy użyciu swojej własnej licencji, możesz skonfigurować punkt końcowy licencji poprzez dodanie odpowiednich zmiennych środowiskowych do tabeli. Pamiętaj, aby w takim przypadku usunąć zaznaczenie domyślnych opcji licencjonowania.

> [!IMPORTANT]
> Opłaty za użycie licencji są naliczane, gdy maszyny wirtualne działają w puli, nawet jeśli maszyny wirtualne nie są obecnie używane w ramach renderowania. Aby uniknąć dodatkowych opłat, przejdź do karty **Pule** i zmień rozmiar puli do 0 węzłów do czasu, gdy będzie konieczne uruchomienie kolejnego zadania renderowania. 
>
>

#### <a name="manage-persistent-pools"></a>Zarządzanie pulami trwałymi

Istniejącą pulą trwałą możesz zarządzać na karcie **Pule**. Wybranie puli z listy spowoduje wyświetlenie bieżącego stanu puli.

Na karcie **Pule** możesz też usunąć pulę i zmienić jej rozmiar, dostosowując liczbę maszyn wirtualnych w puli. Aby uniknąć ponoszenia kosztów w okresie bezczynności, możesz zmienić rozmiar puli na 0 węzłów.

![Wyświetlanie, zmiana rozmiaru i usuwanie pul](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Konfigurowanie zadania renderowania do przesłania

Po określeniu parametrów dla puli, w której zostanie uruchomione zadanie renderowania, skonfiguruj samo zadanie. 

#### <a name="specify-scene-parameters"></a>Określanie parametrów sceny

Wtyczka usługi Batch wykrywa aparat renderowania obecnie używany w programie Maya i wyświetla odpowiednie ustawienia renderowania na karcie **Przesyłanie**. Ustawienia te obejmują ramkę początkową, ramkę końcową, prefiks danych wyjściowych i krok ramki. Ustawienia renderowania pliku sceny możesz przesłonić poprzez określenie innych ustawień we wtyczce. Zmiany wprowadzone w ustawieniach wtyczki nie są utrwalane w ustawieniach renderowania pliku sceny, więc możesz wprowadzać zmiany dla poszczególnych zadań bez potrzeby ponownego przekazywania pliku sceny.

Wtyczka wyświetli ostrzeżenie, jeśli wybrany aparat renderowania w programie Maya nie jest obsługiwany.

Jeśli załadujesz nową scenę, gdy wtyczka jest otwarta, kliknij przycisk **Odśwież**, aby upewnić się, że ustawienia są aktualne.

#### <a name="resolve-asset-paths"></a>Rozpoznawanie ścieżek zasobów

Gdy załadujesz wtyczkę, przeskanuje ona plik sceny pod kątem odwołań do plików zewnętrznych. Te odwołania zostaną wyświetlone na karcie **Zasoby**. Jeśli nie można rozpoznać przywoływanej ścieżki, wtyczka spróbuje znaleźć plik w kilku lokalizacjach domyślnych, w tym:

- Lokalizacja pliku sceny 
- Katalog _sourceimages_ bieżącego projektu
- Bieżący katalog roboczy 

Jeśli nadal nie można zlokalizować zasobu, będzie on wyświetlany na liście z ikoną ostrzeżenia:

![Brakujące zasoby są wyświetlane z ikoną ostrzeżenia](./media/batch-rendering-service/missing_assets.png)

Jeśli znasz lokalizację nierozpoznanego odwołania do pliku, możesz kliknąć ikonę ostrzeżenia. Zostanie wyświetlony monit o dodanie ścieżki wyszukiwania. Następnie wtyczka użyje tej ścieżki wyszukiwania podczas próby rozpoznania wszelkich brakujących zasobów. Liczba dodatkowych ścieżek wyszukiwania nie jest ograniczona.

Po rozpoznaniu odwołania na liście będzie wyświetlana ikona zielonego światła:

![Rozpoznane zasoby są wyświetlane z ikoną zielonego światła](./media/batch-rendering-service/found_assets.png)

Jeśli scena wymaga innych plików, których wtyczka nie wykryła, możesz dodać dodatkowe pliki lub katalogi. Użyj przycisków **Dodaj pliki** i **Dodaj katalog**. Jeśli załadujesz nową scenę, gdy wtyczka jest otwarta, kliknij przycisk **Odśwież**, aby zaktualizować odwołania sceny.

#### <a name="upload-assets-to-an-asset-project"></a>Przekazywanie zasobów do projektu zasobów

Gdy prześlesz zadanie renderowania, przywoływane pliki wyświetlane na karcie **Zasoby** są automatycznie przekazywane do usługi Azure Storage w formie projektu zasobów. Pliki zasobów możesz też przekazać niezależnie od zadania renderowania, korzystając z przycisku **Przekaż** na karcie **Zasoby**. Nazwa projektu zasobów jest określana w polu **Projekt**. Domyślnie jest ona zgodna z bieżącym projektem programu Maya. Podczas przekazywania plików zasobów zachowywana jest lokalna struktura plików. 

Po przekazaniu zasoby mogą być przywoływane przez dowolną liczbę zadań renderowania. Wszystkie przekazane zasoby są dostępne dla dowolnego zadania, które odwołuje się do projektu zasobów, niezależnie od tego, czy są uwzględnione w danej scenie. Aby zmienić projekt zasobów przywoływany przez następne zadanie, zmień nazwę w polu **Projekt** na karcie **Zasoby**. Jeśli nie chcesz przekazywać niektórych przywoływanych plików, usuń ich zaznaczenie przy użyciu zielonego przycisku obok listy.

#### <a name="submit-and-monitor-the-render-job"></a>Przesyłanie i monitorowanie zadania renderowania

Po skonfigurowaniu zadania renderowania we wtyczce kliknij przycisk **Prześlij zadanie** na karcie **Przesyłanie**, aby przesłać zadanie do usługi Batch:

![Przesyłanie zadania renderowania](./media/batch-rendering-service/submit_job.png)

Zadanie w toku możesz monitorować na karcie **Zadania** we wtyczce. Wybierz zadanie z listy, aby wyświetlić bieżący stan zadania. Tej karty możesz też użyć do anulowania i usuwania zadań, a także pobierania danych wyjściowych i dzienników renderowania. 

Aby pobrać dane wyjściowe, zmodyfikuj pole **Dane wyjściowe**, ustawiając żądany katalog docelowy. Kliknij ikonę koła zębatego, aby uruchomić proces w tle, który będzie obserwować zadanie i pobierać dane wyjściowe w miarę postępów: 

![Wyświetlanie stanu zadania i pobieranie danych wyjściowych](./media/batch-rendering-service/jobs.png)

Program Maya możesz zamknąć bez zakłócania procesu pobierania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Batch, zobacz [Uruchamianie wewnętrznie równoległych obciążeń przy użyciu usługi Batch](batch-technical-overview.md).
