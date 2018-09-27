Nie zawsze instalowanie pakietów w sieci Azure przy użyciu programu pip kończy się powodzeniem.  Może się zdarzyć, że dany pakiet nie jest dostępny w indeksie pakietów języka Python.  Może być również wymagany kompilator (kompilator nie jest dostępny na komputerze z aplikacją internetową w usłudze Azure App Service).

W tej części wyjaśniono, jak rozwiązać ten problem.

### <a name="request-wheels"></a>Żądanie plików w formacie Wheel
Jeśli instalacja pakietu wymaga kompilatora, możesz spróbować skontaktować się z właścicielem pakietu i zażądać udostępnienia go jako plików w formacie Wheel.

Dzięki dostępności najnowszych [Microsoft Visual C++ Compiler for Python 2.7][Microsoft Visual C++ Compiler for Python 2.7], jest teraz łatwiejsze tworzenie pakietów korzystających z kodu natywnego języka Python 2.7.

### <a name="build-wheels-requires-windows"></a>Tworzenie plików w formacie Wheel (wymaga systemu Windows)
Uwaga: Korzystając z tej opcji, należy się upewnić, że pakiet jest kompilowany w środowisku Python odpowiadającym platformie/architekturze/wersji używanym w aplikacji internetowej w usłudze Azure App Service (Windows/32-bitowy/2.7 lub 3.4).

Jeśli pakiet nie instaluje się z powodu braku kompilatora, można zainstalować kompilator na komputerze lokalnym i skompilować pakiet w formacie Wheel, który następnie zostanie dołączony do repozytorium.

Użytkownicy systemów Mac/Linux: Jeśli nie masz dostępu do komputera Windows, zobacz [Utwórz maszynę wirtualną Windows uruchomiony] [ Create a Virtual Machine Running Windows] dotyczących sposobu tworzenia maszyny Wirtualnej na platformie Azure.  W zależności od potrzeb można wykorzystać ją przy tworzeniu plików w formacie Wheel, dodać je do repozytorium, po czym usunąć maszynę wirtualną. 

W przypadku języka Python 2.7 można zainstalować [Microsoft Visual C++ Compiler for Python 2.7][Microsoft Visual C++ Compiler for Python 2.7].

W przypadku języka Python 3.4 można zainstalować [programu Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express].

Aby utworzyć pliki w formacie Wheel, będzie potrzebny pakiet Wheel:

    env\scripts\pip install wheel

Aby skompilować zależności, należy użyć programu `pip wheel`:

    env\scripts\pip wheel azure==0.8.4

Spowoduje to utworzenie pliku .whl w folderze \wheelhouse.  Dodaj do repozytorium folder \wheelhouse i pliki w formacie Wheel.

Edytuj plik requirements.txt, aby dodać u góry opcję `--find-links`. Dzięki temu program pip zostanie poinstruowany, aby szukał dokładnego dopasowania w folderze lokalnym przed przejściem do indeksu pakietów języka Python.

    --find-links wheelhouse
    azure==0.8.4

Jeśli chcesz umieścić w folderze \wheelhouse wszystkie zależności i nie używać indeksu pakietów języka Python, możesz wymusić na programie pip ignorowanie indeksu pakietów, dodając parametr `--no-index` u góry pliku requirements.txt.

    --no-index

### <a name="customize-installation"></a>Instalacja niestandardowa
Można dostosować skrypt wdrożenia w celu zainstalowania pakietu w środowisku wirtualnym przy użyciu instalatora alternatywnego, takiego jak easy\_install.  Zapoznaj się z plikiem deploy.cmd, w którym odpowiedni przykład jest zakomentowany.  Upewnij się, że takie pakiety nie są wymienione w pliku requirements.txt, gdyż w przeciwnym razie program pip może je zainstalować.

Dodaj następujące polecenie do skryptu wdrażania:

    env\scripts\easy_install somepackage

Można również użyć programu easy\_install do zainstalowania z programu instalacyjnego w formacie exe (niektóre programy rozpoznają format zip, dlatego są obsługiwane przez program easy\_install).  Dodaj instalator do repozytorium, a następnie wywołaj program easy\_install, przekazując ścieżkę do pliku wykonywalnego.

Dodaj następujące polecenie do skryptu wdrażania:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Dołącza środowisko wirtualne do repozytorium (wymaga systemu Windows)
Uwaga: Korzystając z tej opcji, należy się upewnić, że jest używane środowisko wirtualne odpowiadające platformie/architekturze/wersji używanym w aplikacji internetowej w usłudze Azure App Service (Windows/32-bitowy/2.7 lub 3.4).

W przypadku dołączenia środowiska wirtualnego do repozytorium można zapobiec zarządzaniu środowiskiem wirtualnym w systemie Azure przez skrypt wdrażania, tworząc pusty plik:

    .skipPythonDeployment

Zaleca się usunięcie z aplikacji istniejącego środowiska wirtualnego w celu zapobieżenia pozostawieniu plików z okresu, kiedy środowisko wirtualne było zarządzane automatycznie.

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
