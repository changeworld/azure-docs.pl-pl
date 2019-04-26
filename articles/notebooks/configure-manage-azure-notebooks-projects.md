---
title: Konfigurowanie i zarządzanie projektami notesu platformy Azure
description: Jak zarządzać metadanych projektu, pliki projektu, projekt środowiska i etapy instalacji za pośrednictwem interfejsu użytkownika usługi Azure notesów i bezpośredni dostęp do terminala.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: d1f94c5fd774b51f57da2885d1ccd8eb909cd3c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234774"
---
# <a name="manage-and-configure-projects"></a>Konfigurowanie projektów i zarządzanie nimi

Projekt w notesach Azure jest zasadniczo konfiguracji podstawowej maszyny wirtualnej systemu Linux, w którym uruchamiany notesów programu Jupyter, wraz z opisowymi metadanymi i folderu plików. Pulpit nawigacyjny projektu w notesach Azure umożliwia zarządzanie plikami, a w przeciwnym razie Konfigurowanie właściwości projektu:

- Warstwy obliczeniowej w którym uruchamiany jest projekt, który może być w warstwie bezpłatna lub maszynie wirtualnej platformy Azure.
- Metadane projektu obejmuje nazwę, opis i identyfikator, który jest używany podczas udostępniania projektu i czy projekt jest publicznych lub prywatnych.
- Notes projektu, danych i innych plików, które zarządzają podobnie jak inne systemy plików.
- Projekt środowisko, w którym można zarządzać za pomocą uruchamiania skryptów lub bezpośrednio w terminalu.
- Dzienniki, które dostępu za pomocą terminalu.

> [!Note]
> Funkcje zarządzania i konfiguracji, opisane w tym miejscu są dostępne tylko dla właściciela projektu, który początkowo utworzony projekt. Można jednak klonowanie projektu do własnego konta, w którym to przypadku stać się właścicielem i skonfigurować projekt zgodnie z potrzebami.

Notesy platformy Azure rozpoczyna się maszyny wirtualnej stanowiącej podstawę przy każdym uruchomieniu notesu lub inny plik. Serwer zapisuje pliki i automatycznie zamyka się po 60 minutach braku aktywności. Serwer można również zatrzymać w dowolnym momencie za pomocą **zamknięcia** polecenia (skrót klawiaturowy: h).

## <a name="compute-tier"></a>Warstwa wystąpień obliczeniowych

**Uruchom** listy rozwijanej na pulpicie nawigacyjnym projekt jest wybierania warstwa wystąpień obliczeniowych, w którym uruchamiany jest projekt. Domyślnie projekty są uruchamiane **bezpłatne obliczenia** warstwę, która jest ograniczony do 4 GB pamięci i 1 GB danych, aby zapobiec nadużyciu:

![Obliczenia z listy rozwijanej warstwy na pulpicie nawigacyjnym projekt](media/project-compute-tier-list.png)

Ograniczenia te można pominąć przy użyciu innej maszyny wirtualnej, które zostały aprowizowane w subskrypcji platformy Azure. Należy zainstalować i uruchomić JupyterHub na tej maszynie wirtualnej. Obrazy maszyny wirtualnej do nauki o danych (dowolnego systemu operacyjnego) są dobrych wyborów, ponieważ zawierają one JupyterHub domyślnie.

Po utworzeniu odpowiednio skonfigurowanych maszyn wirtualnych platformy Azure, wybierz **bezpośrednie obliczenia** opcji na liście rozwijanej wyświetli monit o podanie nazwy (w celu wyświetlenia na liście), adres IP maszyny Wirtualnej i port (zazwyczaj 8000, domyślny port, do którego JupyterHub nasłuchuje), a poświadczenia maszyny Wirtualnej:

![Wiersz do zbierania informacji z opcji usługi obliczenia bezpośrednie serwera](media/project-compute-tier-direct.png)

Jeśli następujące warunki są spełnione, listy rozwijanej również pokazuje [maszyny wirtualnej do nauki o danych (DSVM)](/azure/machine-learning/data-science-virtual-machine) wystąpień. (Jeśli którykolwiek z tych warunków nie są spełnione, można nadal połączyć się DSVM przy użyciu opcji obliczeniowych bezpośrednie i wprowadzając wartości uzyskane z portalu Azure.)

- Po zarejestrowaniu się w notesach Azure przy użyciu konta, który używa usługi Azure Active Directory (AAD), takie jak konto firmy.
- Twoje konto jest połączone z subskrypcją platformy Azure.
- Masz co najmniej jednej maszyny wirtualnej w tej subskrypcji z co najmniej dostęp czytnika, używanego przez maszynę wirtualną do nauki o danych do obrazu systemu Linux (Ubuntu).)

![Wystąpienia maszyny wirtualnej do nauki o danych na liście rozwijanej na pulpicie nawigacyjnym projekt](media/project-compute-tier-dsvm.png)

Po wybraniu wystąpienia maszyny wirtualnej DSVM, notesy platformy Azure może monit o poświadczenia określonej maszyny używane podczas tworzenia maszyny Wirtualnej.

Aby utworzyć nowe wystąpienie maszyny wirtualnej DSVM, postępuj zgodnie z instrukcjami [tworzenia maszyny Wirtualnej do nauki o danych Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Użyj **maszyna wirtualna do nauki o danych dla systemu Linux (Ubuntu)** obrazu, jeśli chcesz, aby znajdujący się w rozwijanej liście notesów usługi Azure.  Jeśli z innych przyczyn, należy użyć obrazu Windows lub CentOS, można użyć **bezpośrednie obliczenia** opcję, aby połączyć się z maszyny DSVM ręcznie.

> [!IMPORTANT]
> Korzystając z bezpośredniego obliczeniowych lub do nauki o danych maszyn wirtualnych, notesów, uruchomione na nich musi być całkowicie niezależna. W chwili obecnej, notesy platformy Azure, kopiuje tylko *.ipynb* plików do maszyny Wirtualnej, ale nie kopiuje wszystkie inne pliki w projekcie. W rezultacie notesów działające na innych maszynach wirtualnych nie udało się znaleźć innych plików projektów.
>
> Można obejść ten problem, na dwa sposoby:
>
> 1. Ręcznie skopiuj pliki projektu do maszyny Wirtualnej.
>
> 2. Osadzanie plików w notesie ustawienia uruchamiania przed głównej notesu. W notesie Instalator Utwórz komórce kodu dla każdego pliku, w którym komórka zawiera zawartość pliku. Następnie w górnej części każda komórka Wstaw polecenie `%%writefile <filename>`, gdzie `<filename>` to nazwa pliku do odbierania zawartości. Podczas uruchamiania notesu tworzy tych plików na maszynie Wirtualnej. Aby uzyskać przykład, zobacz [setup.ipynb pliku pokaz wykrywacz Pet Microsoft](https://github.com/Microsoft/connect-petdetector/blob/master/setup.ipynb) (GitHub).
>
>     ![Za pomocą %% writefile polecenia na początku komórkę kodu](media/setup-notebook-writefile-command.png)

## <a name="edit-project-metadata"></a>Edytowanie metadanych projektu

Na pulpicie nawigacyjnym projektu wybierz **ustawienia projektu**, a następnie wybierz **informacji** kartę, która zawiera metadane projektu, zgodnie z opisem w poniższej tabeli. Metadane projektu można zmienić w dowolnym momencie.

| Ustawienie | Opis |
| --- | --- |
| Project name (Nazwa projektu) | Przyjazna nazwa dla projektu, które korzysta z notesów usługi Azure w celach wyświetlania. Na przykład, "Hello World w języku Python". |
| Identyfikator projektu | Niestandardowy identyfikator, który staje się częścią adresu URL, możesz używać do udostępniania projektu. Tego Identyfikatora można użyć tylko litery, cyfry i łączniki, jest ograniczona do 30 znaków i nie może być [zarezerwowany identyfikator projektu](create-clone-jupyter-notebooks.md#reserved-project-ids). Jeśli masz pewności co do użycia, typową Konwencją jest użyć małe wersji nazwa projektu, w którym spacje są przekształcane w łączników, np. "Moja notesu projekt" (obcięty, jeśli to konieczne dopasować limit długości). |
| Publiczne projektu | Jeśli ustawiona, umożliwia każdemu z linkiem do dostępu do projektu. Podczas tworzenia projektu prywatny, usuń zaznaczenie tej opcji. |
| Ukryj klony | Jeśli ustawiona, innych użytkowników nie można wyświetlić listę klonów, które zostały wprowadzone dla tego projektu. Ukrywanie klonów jest przydatne w przypadku projektów, które są współużytkowane z wielu osób, które nie są częścią tej samej organizacji, takich jak podczas korzystania z notesu nauczenia klasy. |

> [!Important]
>
> Zmiana Identyfikatora projektu unieważnia wszelkie linki do projektu, który może został wcześniej udostępniony.

## <a name="manage-project-files"></a>Zarządzanie plikami projektu

Pulpit nawigacyjny projektu pokazuje zawartość systemu folderu projektu. Różne polecenia można użyć do zarządzania tymi plikami.

### <a name="create-new-files-and-folders"></a>Tworzenie nowych plików i folderów

**+ Nowy** polecenia (skrót klawiaturowy: n) tworzy nowe pliki lub foldery. Korzystając z polecenia, najpierw wybierz typ elementu do utworzenia:

| Typ elementu | Opis | Zachowanie polecenia |
| --- | --- | --- |
| **Notes** | Notes Jupyter | Wyświetla okno podręczne, w którym należy określić nazwę pliku i język notesu. |
| **Folder** | Podfolder | Tworzy pole edycji na liście plików projektu, do którego należy wprowadzić nazwę folderu. |
| **Pusty plik** | Plik, w którym można przechowywać dowolną zawartość, takie jak tekst, data itp. | Tworzy pole edycji na liście plików projektu, do którego należy wprowadzić nazwę pliku. |
| **Markdown** | Plik języka znaczników Markdown. | Tworzy pole edycji na liście plików projektu, do którego należy wprowadzić nazwę pliku. |

### <a name="upload-files"></a>Przekazywanie plików

**Przekazywanie** polecenia oferuje dwie opcje importowania danych z innych lokalizacji: **Z adresu URL** i **z komputera**. Aby uzyskać więcej informacji, zobacz [Praca z plikami danych w projektach notesu Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Wybierz polecenia specyficzne dla pliku

Każdy element na liście plik projektu zawiera poleceń w menu kontekstowym kliknij prawym przyciskiem myszy:

![Polecenia w menu kontekstowym pliku](media/project-file-commands.png)

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Uruchom polecenie | r (lub kliknij przycisk) | Uruchamia plik notesu. Inne typy plików są otwarte do wyświetlenia.  |
| Kopiuj łącze | Y | Kopiuje łącza do pliku do Schowka. |
| Uruchom w laboratorium Jupyter | "j" | Jest uruchamiany Notes w JupyterLab, który jest bardziej zorientowanego na dewelopera interfejsu niż zazwyczaj zapewnia Jupyter. |
| Wersja zapoznawcza | p | Otwiera podgląd pliku; w formacie HTML w przypadku notesów korzystania z wersji zapoznawczej jest tylko do odczytu renderowania notesu. Aby uzyskać więcej informacji, zobacz [Podgląd](#preview) sekcji. |
| Edytuj plik | Czy mogę | Zostanie otwarty plik do edycji. |
| Do pobrania | d. | Pobieranie pliku zip, który zawiera plik lub zawartość folderu. |
| Zmiana nazwy | a | Wyświetla monit o nową nazwę dla pliku lub folderu. |
| Usuwanie | x | Monituje o potwierdzenie, a następnie trwale usuwa plik z projektu. Usunięcie nie może być cofnięte. |
| Move | ś | Przenosi plik do innego folderu, w tym samym projekcie. |

#### <a name="preview"></a>Wersja zapoznawcza

Podgląd pliku lub notes jest widok tylko do odczytu zawartości; Uruchamianie notesu komórek jest wyłączona. Podgląd jest wyświetlany, aby każda osoba, która zawiera link do pliku lub Notes, ale nie zalogował się do notesów usługi Azure. Po zalogowaniu użytkownika można sklonować notesu na swoje konto lub będą oni mogli pobrać notesu na ich komputerach lokalnych.

Na stronie (wersja zapoznawcza) obsługuje kilka poleceń narzędzi za pomocą skrótów klawiaturowych:

| Polecenie | Skrót klawiaturowy | Akcja |
| --- | --- | --- |
| Udostępnij | s | Wyświetla udostępniania okna podręcznego, z którego można uzyskać link, Udostępnij w mediach społecznościowych, uzyskać osadzenia HTML i Wyślij wiadomość e-mail. |
| Klonowanie | c  | Klonuj Notes do Twojego konta. |
| Uruchom polecenie | r | Uruchamia Notes, jeśli możesz to zrobić. |
| Do pobrania | d. | Pobiera kopię notesu. |

## <a name="configure-the-project-environment"></a>Konfigurowanie środowiska programu project

Istnieją trzy sposoby, aby skonfigurować środowisko maszyny wirtualnej stanowiącej podstawę, w którym są uruchomione w notesach programu:

- Obejmują skrypt jednorazowe inicjowanie
- Użyj ustawień środowiska projektu, aby określić kroki konfiguracji
- Dostęp do maszyny wirtualnej za pomocą terminalu.

Wszystkie rodzaje konfiguracji projektu są stosowane zawsze, gdy maszyna wirtualna jest uruchomiona i dlatego ma wpływ na wszystkie notesy w projekcie.

### <a name="one-time-initialization-script"></a>Jednorazowe inicjowanie skryptu

Notesy platformy Azure po raz pierwszy zostanie utworzony serwer dla projektu, szuka pliku w projekcie o nazwie *aznbsetup.sh*. Jeśli ten plik jest obecny, notesy platformy Azure, uruchomi go. Dane wyjściowe skryptu są przechowywane w folderze projektu jako *. aznbsetup.log*.

### <a name="environment-setup-steps"></a>Kroki konfiguracji środowiska

Ustawienia środowiska projektu umożliwia tworzenie poszczególne kroki, które skonfigurowanie środowiska.

Na pulpicie nawigacyjnym projektu wybierz **ustawienia projektu**, a następnie wybierz **środowiska** karty, w którym można dodać, usuwania i modyfikowania kolejne kroki konfiguracji projektu:

![Okno podręczne z ustawień projektu z wybraną kartą środowiska](media/project-settings-environment-steps.png)

Aby dodać krok, najpierw wybierz **+ Dodaj**, następnie wybierz typ kroku w **operacji** listy rozwijanej:

![Selektor operacji dla nowego kroku konfiguracji środowiska](media/project-settings-environment-details.png)

Informacje, które następnie projektu zależy od typu operacji, które wybrałeś:

- **Plik Requirements.txt**: Na drugiej liście rozwijanej wybierz *requirements.txt* pliku, który znajduje się już w projekcie. Następnie wybierz wersję języka Python z trzeciej listy rozwijanej, która pojawia się. Za pomocą *requirements.txt* plików i przebiegi notesów usługi Azure `pip install -r` z *requirements.txt* pliku podczas uruchamiania serwer notesu. Nie trzeba jawnie instalować pakiety z w obrębie samego notesu.

- **Skrypt powłoki**: Na drugiej liście rozwijanej wybierz skrypt powłoki bash w projekcie (zazwyczaj plik z *SH* rozszerzenia) zawierający wszystkie polecenia, które chcesz uruchomić, aby zainicjować środowisko.

- **Environment.yml**: Na drugiej liście rozwijanej wybierz *environments.yml* plików dla projektów języka Python przy użyciu środowiska conda.

Po zakończeniu dodawania kroków, wybierz **Zapisz**.

### <a name="use-the-terminal"></a>Użyj terminalu

Na pulpicie nawigacyjnym projekt **terminalu** polecenie otwiera terminalu systemu Linux, zapewniająca bezpośredni dostęp do serwera. W terminalu można pobrać dane, Edytuj lub zarządzania plikami, kontroli procesów i nawet za pomocą narzędzi, takich jak vi i nano.

> [!Note]
> Jeśli masz skrypty uruchamiania w środowisku projektu, otwierając terminal może być wyświetlany komunikat wskazujący, że czy konfiguracja jest nadal w toku.

Można wydać dowolne standardowe polecenia systemu Linux w terminalu. Można również użyć `ls` w głównym folderze, aby zobaczyć różnych środowiskach, które istnieją na maszynie wirtualnej, takie jak *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*, i *R*, wraz z *projektu* folder zawierający projekt:

![Projekt terminalu w notesach platformy Azure](media/project-terminal.png)

Wpływ na określone środowisko, najpierw zmień katalogi do tego folderu w środowisku.

Dla środowiska Python znajdziesz `pip` i `conda` w *bin* folderu dla każdego środowiska. Umożliwia także wbudowane aliasów dla środowisk:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Zmiany wprowadzone do serwera dotyczą tylko w bieżącej sesji, z wyjątkiem plików i folderów, możesz utworzyć w *projektu* sam folder. Na przykład, edytując plik w folderze projektu jest zachowywane między sesjami, ale pakietów z `pip install` nie są.

> [!Note]
> Jeśli używasz `python` lub `python3`, wywołaj Python, które nie są używane do notesów wersje zainstalowany system. Nie masz uprawnienia dla operacji, takich jak `pip install` albo, dlatego należy stosować aliasy poszczególnym wersjom.

## <a name="access-notebook-logs"></a>Uzyskiwanie dostępu do dzienników notesu

Jeśli napotkasz problemy podczas uruchamiania notesu, dane wyjściowe w aplikacji Jupyter są przechowywane w folderze o nazwie *. nb.log*. Możesz uzyskać dostęp tych dzienników za pośrednictwem **terminalu** polecenia lub pulpit nawigacyjny projektu.

Zwykle po uruchomieniu programu Jupyter lokalnie mogły zostać rozpoczęte go w oknie terminala. Okno terminalu pokazuje dane wyjściowe, takie jak stan jądra.

Aby wyświetlić dzienniki, wpisz następujące polecenie w terminalu:

```bash
cat .nb.log
```

Umożliwia także polecenia w komórce kodu w notesie Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Kolejne kroki

- [Instrukcje: Praca z plikami danych projektu](work-with-project-data-files.md)
- [Dostęp do danych w chmurze w notesie](access-data-resources-jupyter-notebooks.md)
