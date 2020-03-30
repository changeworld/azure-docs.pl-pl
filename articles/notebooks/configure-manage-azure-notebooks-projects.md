---
title: Konfigurowanie notesów platformy Azure Preview i zarządzanie nimi
description: Dowiedz się, jak zarządzać metadanymi projektu, plikami projektu, środowiskiem projektu i krokami konfiguracji za pośrednictwem interfejsu użytkownika notesów platformy Azure i bezpośredniego dostępu do terminali.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280601"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Zarządzanie projektami i konfigurowanie ich w usłudze Azure Notebooks Preview

Projekt w usłudze Azure Notebooks Preview jest zasadniczo konfiguracją podstawowej maszyny wirtualnej systemu Linux, w której działają notesy Jupyter, wraz z folderem plików i metadanymi opisowymi. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Pulpit nawigacyjny projektu w notesach platformy Azure umożliwia zarządzanie plikami i w inny sposób konfigurowanie charakterystyk projektu:

- Warstwa obliczeniowa, na której uruchamia się projekt, która może być warstwą bezpłatną lub maszyną wirtualną platformy Azure.
- Metadane projektu, który zawiera nazwę, opis, identyfikator, który jest używany podczas udostępniania projektu i czy projekt jest publiczny lub prywatny.
- Notes, dane i inne pliki projektu, którymi zarządzasz, jak każdy inny system plików.
- Środowisko projektu, którym zarządzasz za pomocą skryptów startowych lub bezpośrednio za pośrednictwem terminala.
- Dzienniki, do których uzyskujesz dostęp przez terminal.

> [!Note]
> Opisane w tym miejscu funkcje zarządzania i konfiguracji są dostępne tylko dla właściciela projektu, który początkowo utworzył projekt. Możesz jednak sklonować projekt na własne konto, w którym to przypadku stajesz się właścicielem i możesz skonfigurować projekt zgodnie z potrzebami.

Notesy platformy Azure uruchamia podstawowej maszyny wirtualnej przy każdym uruchomieniu notesu lub innego pliku. Serwer automatycznie zapisuje pliki i wyłącza się po 60 minutach braku aktywności. Serwer można również zatrzymać w dowolnym momencie za pomocą polecenia **Shutdown** (skrót klawiaturowy: h).

## <a name="compute-tier"></a>Warstwa obliczeniowa

Domyślnie projekty są uruchamiane w warstwie **Wolne obliczenia,** która jest ograniczona do 4 GB pamięci i 1 GB danych, aby zapobiec nadużyciom. Można pominąć te ograniczenia i zwiększyć moc obliczeniową przy użyciu innej maszyny wirtualnej, która została aprowizowana w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Jak używać maszyn wirtualnych do nauki o danych](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Edytowanie metadanych projektu

Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**, a następnie wybierz kartę **Informacje,** która zawiera metadane projektu, jak opisano w poniższej tabeli. Metadane projektu można zmienić w dowolnym momencie.

| Ustawienie | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa projektu, który używa notesów platformy Azure do celów wyświetlania. Na przykład "Hello World in Python". |
| Identyfikator projektu | Niestandardowy identyfikator, który staje się częścią adresu URL używanego do udostępniania projektu. Ten identyfikator może używać tylko liter, cyfr i łączników, jest ograniczony do 30 znaków i nie może być [zastrzeżonym identyfikatorem projektu.](create-clone-jupyter-notebooks.md#reserved-project-ids) Jeśli nie masz pewności, czego użyć, wspólną konwencją jest użycie małych liter nazwy projektu, w której spacje są przekształcane w łączniki, takie jak "mój notes-project" (obcięty, jeśli to konieczne, aby dopasować limit długości). |
| Projekt publiczny | Jeśli jest ustawiona, umożliwia każdemu, kto ma łącze, dostęp do projektu. Podczas tworzenia projektu prywatnego wyczyść tę opcję. |
| Ukrywanie klonów | Jeśli jest ustawiona, inni użytkownicy nie widzą listy klonów, które zostały wprowadzone dla tego projektu. Ukrywanie klonów jest przydatne w przypadku projektów udostępnianych wielu osobom, które nie należą do tej samej organizacji, na przykład podczas korzystania z notesu do nauczania klasy. |

> [!Important]
>
> Zmiana identyfikatora projektu unieważnia wszystkie łącza do projektu, które mogły zostać udostępnione wcześniej.

## <a name="manage-project-files"></a>Zarządzanie plikami projektu

Pulpit nawigacyjny projektu pokazuje zawartość systemu folderów projektu. Do zarządzania tymi plikami można używać różnych poleceń.

### <a name="create-new-files-and-folders"></a>Tworzenie nowych plików i folderów

Polecenie **+ Nowy** (skrót klawiaturowy: n) tworzy nowe pliki lub foldery. Podczas korzystania z polecenia należy najpierw wybrać typ elementu do utworzenia:

| Typ towaru | Opis | Zachowanie polecenia |
| --- | --- | --- |
| **Notes** | Notatnik Jupytera | Wyświetla okno podręczne, w którym określa się nazwę pliku i język notesu. |
| **Folder** | Podfolder | Tworzy pole edycji na liście plików projektu, do którego wprowadza się nazwę folderu. |
| **Pusty plik** | Plik, w którym można przechowywać dowolną zawartość, taką jak tekst, dane itp. | Tworzy pole edycji na liście plików projektu, do którego wprowadza się nazwę pliku. |
| **Markdown** | Plik Markdown. | Tworzy pole edycji na liście plików projektu, do którego wprowadza się nazwę pliku. |

### <a name="upload-files"></a>Przekazywanie plików

Polecenie **Przekaż** udostępnia dwie opcje importowania danych z innych lokalizacji: **Z adresu URL** i Z **komputera**. Aby uzyskać więcej informacji, zobacz [Praca z plikami danych w projektach notesu platformy Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Wybieranie poleceń specyficznych dla pliku

Każdy element na liście plików projektu udostępnia polecenia za pomocą menu kontekstowego, które można kliknąć prawym przyciskiem myszy:

![Polecenia w menu kontekstowym pliku](media/project-file-commands.png)

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Run | r (lub kliknij) | Uruchamia plik notesu. Inne typy plików są otwierane do przeglądania.  |
| Kopiuj link | t | Kopiuje łącze do pliku do schowka. |
| Bieg w Jupyter Lab | J | Uruchamia notebooka w JupyterLab, który jest bardziej zorientowany na programistę interfejs niż Jupyter zwykle zapewnia. |
| Wersja zapoznawcza | p | Otwiera podgląd pliku w formacie HTML; w przypadku notesów podgląd jest renderowaniem notesu tylko do odczytu. Aby uzyskać więcej informacji, zobacz sekcję [Podgląd.](#preview) |
| Edytuj plik | mogę | Otwiera plik do edycji. |
| Pobierz | d | Pobiera plik zip zawierający plik lub zawartość folderu. |
| Zmiana nazwy | a | Monituje o nową nazwę pliku lub folderu. |
| Usuń | x | Monituje o potwierdzenie, a następnie trwale usuwa plik z projektu. Usunięcia nie można cofnąć. |
| Move | m | Przenosi plik do innego folderu w tym samym projekcie. |

#### <a name="preview"></a>Wersja zapoznawcza

Podgląd pliku lub notesu jest widokiem zawartości tylko do odczytu; uruchomione komórki notesu są wyłączone. Podgląd jest wyświetlany każdemu, kto ma łącze do pliku lub notesu, ale nie zalogował się do notesów platformy Azure. Po zalogowaniu użytkownik może sklonować notes na własne konto lub pobrać notes na komputer lokalny.

Strona podglądu obsługuje kilka poleceń paska narzędzi za pomocą skrótów klawiaturowych:

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Udostępnij | s | Wyświetla okno podręczne udostępniania, z którego można uzyskać łącze, udostępnić je w mediach społecznościowych, uzyskać kod HTML do osadzania i wysłać wiadomość e-mail. |
| Klonowanie | c  | Sklonuj notes na swoim koncie. |
| Run | r | Uruchamia notes, jeśli możesz to zrobić. |
| Pobierz | d | Pobiera kopię notesu. |

## <a name="configure-the-project-environment"></a>Konfigurowanie środowiska projektu

Istnieją trzy sposoby konfigurowania środowiska podstawowej maszyny wirtualnej, w którym są uruchamiane notesy:

- Dołącz skrypt jednorazowego inicjowania
- Określanie kroków konfiguracji za pomocą ustawień środowiska projektu
- Dostęp do maszyny wirtualnej za pośrednictwem terminala.

Wszystkie formy konfiguracji projektu są stosowane przy każdym uruchomieniu maszyny wirtualnej, a tym samym wpływa na wszystkie notesy w ramach projektu.

### <a name="one-time-initialization-script"></a>Skrypt jednorazowego inicjowania

Po raz pierwszy notesy platformy Azure tworzy serwer dla projektu, szuka pliku w projekcie o nazwie *aznbsetup.sh*. Jeśli ten plik jest obecny, notesy platformy Azure uruchamia go. Dane wyjściowe skryptu są przechowywane w folderze projektu jako *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Kroki konfiguracji środowiska

Ustawienia środowiska projektu służą do tworzenia poszczególnych kroków, które konfigurują środowisko.

Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**, a następnie wybierz kartę **Środowisko,** na której można dodać, usunąć i zmodyfikować kroki konfiguracji projektu:

![Wyskakujące okienko Ustawień projektu z wybraną kartą Środowisko](media/project-settings-environment-steps.png)

Aby dodać krok, najpierw wybierz **+ Dodaj**, a następnie wybierz typ kroku na liście rozwijanej **Operacja:**

![Selektor operacji dla nowego kroku konfiguracji środowiska](media/project-settings-environment-details.png)

Informacje, które następnie projektujesz, zależą od wybranego typu operacji:

- **Requirements.txt**: Na drugiej liście rozwijanej wybierz plik *requirements.txt,* który jest już w projekcie. Następnie wybierz wersję Języka Python z trzeciej listy rozwijanej, która się pojawi. Przy użyciu pliku *requirements.txt* notesy `pip install -r` platformy Azure są uruchamiane z plikiem *requirements.txt* podczas uruchamiania serwera notesu. Nie trzeba jawnie instalować pakietów z samego notesu.

- **Skrypt powłoki:** Na drugiej liście rozwijanej wybierz skrypt powłoki bash w projekcie (zazwyczaj plik z rozszerzeniem *.sh),* który zawiera wszystkie polecenia, które chcesz uruchomić w celu zainicjowania środowiska.

- **Environment.yml**: Na drugiej liście rozwijanej wybierz plik *environments.yml* dla projektów języka Python przy użyciu środowiska conda.

   > [!WARNING]
   > Ponieważ jest to usługa w wersji zapoznawczej w `Environment.yml` fazie rozwoju, obecnie istnieje znany problem, w którym ustawienie nie zostanie zastosowane do projektu zgodnie z oczekiwaniami. Projekt i notesy Jupyter wewnątrz nie ładują pliku określonego środowiska w chwili obecnej.

Po zakończeniu dodawania wybierz pozycję **Zapisz**.

### <a name="use-the-terminal"></a>Korzystanie z terminala

Na pulpicie nawigacyjnym projektu polecenie **Terminal** otwiera terminal linuxowy, który zapewnia bezpośredni dostęp do serwera. W terminalu można pobierać dane, edytować lub zarządzać plikami, sprawdzać procesy, a nawet korzystać z narzędzi takich jak vi i nano.

> [!Note]
> Jeśli masz skrypty startowe w środowisku projektu, otwarcie terminala może wyświetlać komunikat informujący, że konfiguracja jest nadal w toku.

W terminalu można wydawać dowolne standardowe polecenia Linuksa. Można również `ls` użyć w folderze macierzystym, aby wyświetlić różne środowiska, które istnieją na maszynie wirtualnej, takie jak *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*i *R*, wraz z folderem *projektu,* który zawiera projekt:

![Terminal projektu w notesach platformy Azure](media/project-terminal.png)

Aby wpłynąć na określone środowisko, należy najpierw zmienić katalogi w tym folderze środowiska.

W środowiskach Języka Python `pip` można `conda` znaleźć i w folderze *bin* każdego środowiska. Można również użyć wbudowanych aliasów dla środowisk:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Zmiany wprowadzone na serwerze dotyczą tylko bieżącej sesji, z wyjątkiem plików i folderów utworzonych w samym folderze *projektu.* Na przykład edytowanie pliku w folderze projektu jest zachowywane `pip install` między sesjami, ale pakiety z nie są.

> [!Note]
> Jeśli `python` używasz `python3`lub , wywołać system zainstalowanych wersji języka Python, które nie są używane dla notesów. Nie masz uprawnień do operacji, takich jak `pip install` albo, więc należy użyć aliasów specyficznych dla wersji.

## <a name="access-notebook-logs"></a>Dostęp do dzienników notesu

Jeśli podczas uruchamiania notesu występują problemy, dane wyjściowe z programu Jupyter są przechowywane w folderze o nazwie *.nb.log*. Dostęp do tych dzienników można uzyskać za pomocą polecenia **Terminal** lub pulpitu nawigacyjnego projektu.

Zazwyczaj podczas uruchamiania Jupyter lokalnie być może został uruchomiony z okna terminala. Okno terminala pokazuje dane wyjściowe, takie jak stan jądra.

Aby wyświetlić dzienniki, użyj następującego polecenia w terminalu:

```bash
cat .nb.log
```

Można również użyć polecenia z komórki kodu w notesie języka Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Następne kroki

- [Jak: Praca z plikami danych projektu](work-with-project-data-files.md)
- [Dostęp do danych z chmury w notesie](access-data-resources-jupyter-notebooks.md)
