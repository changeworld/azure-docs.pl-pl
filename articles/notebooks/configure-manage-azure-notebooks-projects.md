---
title: Konfigurowanie i zarządzanie Azure Notebooks wersja zapoznawcza
description: Dowiedz się, jak zarządzać metadanymi projektu, plikami projektu, środowiskiem projektu i instrukcjami konfiguracji za pośrednictwem interfejsu użytkownika Azure Notebooks i bezpośredniego dostępu do terminalu.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280601"
---
# <a name="a-idmanage-and-configure-projects--manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" /> zarządzanie projektami i konfigurowanie ich w programie Azure Notebooks Preview

Projekt w programie Azure Notebooks Preview jest zasadniczo konfiguracją podstawowej maszyny wirtualnej z systemem Linux, w której działają notesy Jupyter oraz folder plików i metadane opisowe. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Pulpit nawigacyjny projektu w notesach Azure umożliwia zarządzanie plikami, a w przeciwnym razie Konfigurowanie właściwości projektu:

- Warstwa obliczeniowa, na której działa projekt, który może być warstwą bezpłatną lub maszyną wirtualną platformy Azure.
- Metadane projektu, w tym nazwa, opis, identyfikator, który jest używany podczas udostępniania projektu i czy projekt jest publiczny czy prywatny.
- Notes, dane i inne pliki projektu, którymi można zarządzać jak każdy inny system plików.
- Środowisko projektu, którymi można zarządzać za pomocą skryptów uruchamiania lub bezpośrednio za pomocą terminalu.
- Dzienniki, do których można uzyskać dostęp za pomocą terminalu.

> [!Note]
> Opisane w tym miejscu funkcje zarządzania i konfiguracji są dostępne tylko dla właściciela projektu, który początkowo utworzył projekt. Można jednak sklonować projekt na własne konto, w takim przypadku staje się właścicielem i może skonfigurować projekt zgodnie z potrzebami.

Notesy platformy Azure rozpoczyna się maszyny wirtualnej stanowiącej podstawę przy każdym uruchomieniu notesu lub inny plik. Serwer zapisuje pliki i automatycznie zamyka się po 60 minutach braku aktywności. Możesz również zatrzymać serwer w dowolnym momencie za pomocą polecenia **Shutdown** (skrót klawiaturowy: h).

## <a name="compute-tier"></a>Warstwa obliczeniowa

Domyślnie projekty są uruchamiane w **bezpłatnej warstwie obliczeniowej** , co jest ograniczone do 4 GB pamięci i 1 GB danych, aby zapobiec nadużyciu. Można pominąć te ograniczenia i zwiększyć moc obliczeniową przy użyciu innej maszyny wirtualnej, która została zainicjowana w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [jak używać analizy danych Virtual Machines](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Edytowanie metadanych projektu

Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**, a następnie wybierz kartę **informacje** , która zawiera metadane projektu, zgodnie z opisem w poniższej tabeli. Metadane projektu można zmienić w dowolnym momencie.

| Ustawienie | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa dla projektu, które korzysta z notesów usługi Azure w celach wyświetlania. Na przykład, "Hello World w języku Python". |
| Identyfikator projektu | Niestandardowy identyfikator, który staje się częścią adresu URL, możesz używać do udostępniania projektu. Ten identyfikator może korzystać tylko z liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zarezerwowanym identyfikatorem projektu](create-clone-jupyter-notebooks.md#reserved-project-ids). Jeśli masz pewności co do użycia, typową Konwencją jest użyć małe wersji nazwa projektu, w którym spacje są przekształcane w łączników, np. "Moja notesu projekt" (obcięty, jeśli to konieczne dopasować limit długości). |
| Publiczne projektu | Jeśli ustawiona, umożliwia każdemu z linkiem do dostępu do projektu. Podczas tworzenia projektu prywatny, usuń zaznaczenie tej opcji. |
| Ukryj klony | Jeśli ustawiona, innych użytkowników nie można wyświetlić listę klonów, które zostały wprowadzone dla tego projektu. Ukrywanie klonów jest przydatne w przypadku projektów, które są współużytkowane z wielu osób, które nie są częścią tej samej organizacji, takich jak podczas korzystania z notesu nauczenia klasy. |

> [!Important]
>
> Zmiana Identyfikatora projektu unieważnia wszelkie linki do projektu, który może został wcześniej udostępniony.

## <a name="manage-project-files"></a>Zarządzanie plikami projektu

Pulpit nawigacyjny projektu pokazuje zawartość systemu folderu projektu. Różne polecenia można użyć do zarządzania tymi plikami.

### <a name="create-new-files-and-folders"></a>Tworzenie nowych plików i folderów

**+ Nowe** polecenie (skrót klawiaturowy: n) tworzy nowe pliki lub foldery. Korzystając z polecenia, najpierw wybierz typ elementu do utworzenia:

| Typ elementu | Opis | Zachowanie polecenia |
| --- | --- | --- |
| **Notesu** | Notes Jupyter | Wyświetla okno podręczne, w którym należy określić nazwę pliku i język notesu. |
| **Folder** | Podfolder | Tworzy pole edycji na liście plików projektu, do którego należy wprowadzić nazwę folderu. |
| **Pusty plik** | Plik, w którym można przechowywać dowolną zawartość, takie jak tekst, data itp. | Tworzy pole edycji na liście plików projektu, do którego należy wprowadzić nazwę pliku. |
| **Promocji** | Plik języka znaczników Markdown. | Tworzy pole edycji na liście plików projektu, do którego należy wprowadzić nazwę pliku. |

### <a name="upload-files"></a>Przekazywanie plików

Polecenie **upload** oferuje dwie opcje importowania danych z innych lokalizacji: **od adresu URL** i **z komputera**. Aby uzyskać więcej informacji, zobacz artykuł [Pracuj z plikami danych w projektach notesów platformy Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Wybierz polecenia specyficzne dla pliku

Każdy element na liście plik projektu zawiera poleceń w menu kontekstowym kliknij prawym przyciskiem myszy:

![Polecenia w menu kontekstowym pliku](media/project-file-commands.png)

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Uruchom polecenie | r (lub kliknij przycisk) | Uruchamia plik notesu. Inne typy plików są otwarte do wyświetlenia.  |
| Kopiuj łącze | {1&gt;y&lt;1} | Kopiuje łącza do pliku do Schowka. |
| Uruchom w laboratorium Jupyter | "j" | Jest uruchamiany Notes w JupyterLab, który jest bardziej zorientowanego na dewelopera interfejsu niż zazwyczaj zapewnia Jupyter. |
| Wersja zapoznawcza | p | Otwiera podgląd pliku; w formacie HTML w przypadku notesów korzystania z wersji zapoznawczej jest tylko do odczytu renderowania notesu. Aby uzyskać więcej informacji, zobacz sekcję [wersja zapoznawcza](#preview) . |
| Edytuj plik | Czy mogę | Zostanie otwarty plik do edycji. |
| Pobieranie | {1&gt;d&lt;1} | Pobieranie pliku zip, który zawiera plik lub zawartość folderu. |
| Zmień nazwę | a | Wyświetla monit o nową nazwę dla pliku lub folderu. |
| Usuń | x | Monituje o potwierdzenie, a następnie trwale usuwa plik z projektu. Usunięcie nie może być cofnięte. |
| Move | ś | Przenosi plik do innego folderu, w tym samym projekcie. |

#### <a name="preview"></a>Wersja zapoznawcza

Podgląd pliku lub notes jest widok tylko do odczytu zawartości; Uruchamianie notesu komórek jest wyłączona. Podgląd jest wyświetlany, aby każda osoba, która zawiera link do pliku lub Notes, ale nie zalogował się do notesów usługi Azure. Po zalogowaniu użytkownika można sklonować notesu na swoje konto lub będą oni mogli pobrać notesu na ich komputerach lokalnych.

Na stronie (wersja zapoznawcza) obsługuje kilka poleceń narzędzi za pomocą skrótów klawiaturowych:

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Udział | s | Wyświetla udostępniania okna podręcznego, z którego można uzyskać link, Udostępnij w mediach społecznościowych, uzyskać osadzenia HTML i Wyślij wiadomość e-mail. |
| Klonowanie | c  | Klonuj Notes do Twojego konta. |
| Uruchom polecenie | r | Uruchamia Notes, jeśli możesz to zrobić. |
| Pobieranie | {1&gt;d&lt;1} | Pobiera kopię notesu. |

## <a name="configure-the-project-environment"></a>Konfigurowanie środowiska programu project

Istnieją trzy sposoby, aby skonfigurować środowisko maszyny wirtualnej stanowiącej podstawę, w którym są uruchomione w notesach programu:

- Obejmują skrypt jednorazowe inicjowanie
- Użyj ustawień środowiska projektu, aby określić kroki konfiguracji
- Dostęp do maszyny wirtualnej za pomocą terminalu.

Wszystkie rodzaje konfiguracji projektu są stosowane zawsze, gdy maszyna wirtualna jest uruchomiona i dlatego ma wpływ na wszystkie notesy w projekcie.

### <a name="one-time-initialization-script"></a>Jednorazowe inicjowanie skryptu

Pierwszy raz Azure Notebooks tworzy serwer dla projektu, szuka pliku w projekcie o nazwie *aznbsetup.sh*. Jeśli ten plik jest obecny, Azure Notebooks go uruchamiać. Dane wyjściowe skryptu są przechowywane w folderze projektu jako *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Kroki konfiguracji środowiska

Ustawienia środowiska projektu umożliwia tworzenie poszczególne kroki, które skonfigurowanie środowiska.

Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**, a następnie wybierz kartę **środowisko** , w której można dodawać, usuwać i modyfikować kroki instalacji projektu:

![Okno podręczne z ustawień projektu z wybraną kartą środowiska](media/project-settings-environment-steps.png)

Aby dodać krok, najpierw wybierz pozycję **+ Dodaj**, a następnie wybierz typ kroku z listy rozwijanej **operacja** :

![Selektor operacji dla nowego kroku konfiguracji środowiska](media/project-settings-environment-details.png)

Informacje, które następnie projektu zależy od typu operacji, które wybrałeś:

- **Requirements. txt**: na drugiej liście rozwijanej wybierz plik *Requirements. txt* , który znajduje się już w projekcie. Następnie wybierz wersję języka Python z trzeciej listy rozwijanej, która pojawia się. Przy użyciu pliku *Requirements. txt* Azure Notebooks uruchamia `pip install -r` z plikiem *Requirements. txt* podczas uruchamiania serwera notesu. Nie trzeba jawnie instalować pakiety z w obrębie samego notesu.

- **Skrypt powłoki**: na drugiej liście rozwijanej wybierz skrypt powłoki bash w projekcie (zazwyczaj plik z rozszerzeniem *. sh* ) zawierający wszystkie polecenia, które chcesz uruchomić w celu zainicjowania środowiska.

- **Środowisko. yml**: na drugiej liście rozwijanej wybierz plik Environments *. yml* dla projektów języka Python korzystających ze środowiska Conda.

   > [!WARNING]
   > Ponieważ jest to usługa w wersji zapoznawczej, jest obecnie znany problem, w którym ustawienie `Environment.yml` nie zostanie zastosowane do projektu zgodnie z oczekiwaniami. Projekty i notesy Jupyter w programie nie ładują teraz określonego pliku środowiska.

Po zakończeniu dodawania kroków wybierz pozycję **Zapisz**.

### <a name="use-the-terminal"></a>Użyj terminalu

Na pulpicie nawigacyjnym projektu polecenie **terminalu** otwiera terminal systemu Linux, który zapewnia bezpośredni dostęp do serwera. W terminalu można pobrać dane, Edytuj lub zarządzania plikami, kontroli procesów i nawet za pomocą narzędzi, takich jak vi i nano.

> [!Note]
> Jeśli masz skrypty uruchamiania w środowisku projektu, otwierając terminal może być wyświetlany komunikat wskazujący, że czy konfiguracja jest nadal w toku.

Można wydać dowolne standardowe polecenia systemu Linux w terminalu. Możesz również użyć `ls` w folderze głównym, aby zobaczyć różne środowiska, które istnieją na maszynie wirtualnej, takie jak *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*i *R*, wraz z folderem *projektu* zawierającym projekt:

![Projekt terminalu w notesach platformy Azure](media/project-terminal.png)

Wpływ na określone środowisko, najpierw zmień katalogi do tego folderu w środowisku.

W środowiskach języka Python można znaleźć `pip` i `conda` w folderze *bin* każdego środowiska. Umożliwia także wbudowane aliasów dla środowisk:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Zmiany wprowadzone na serwerze dotyczą tylko bieżącej sesji, z wyjątkiem plików i folderów tworzonych w folderze *projektu* . Na przykład edytowanie pliku w folderze projektu jest utrwalane między sesjami, ale pakiety z `pip install` nie są.

> [!Note]
> W przypadku używania `python` lub `python3`, należy wywołać wersje języka Python zainstalowane w systemie, które nie są używane w notesach. Nie masz uprawnień do operacji, takich jak `pip install`, dlatego upewnij się, że używasz aliasów specyficznych dla wersji.

## <a name="access-notebook-logs"></a>Uzyskiwanie dostępu do dzienników notesu

Jeśli podczas uruchamiania notesu wystąpią problemy, dane wyjściowe z Jupyter są przechowywane w folderze o nazwie *. NB. log*. Dostęp do tych dzienników można uzyskać za pomocą polecenia **terminala** lub pulpitu nawigacyjnego projektu.

Zwykle po uruchomieniu programu Jupyter lokalnie mogły zostać rozpoczęte go w oknie terminala. Okno terminalu pokazuje dane wyjściowe, takie jak stan jądra.

Aby wyświetlić dzienniki, wpisz następujące polecenie w terminalu:

```bash
cat .nb.log
```

Umożliwia także polecenia w komórce kodu w notesie Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: współdziałanie z plikami danych projektu](work-with-project-data-files.md)
- [Dostęp do danych w chmurze w notesie](access-data-resources-jupyter-notebooks.md)
