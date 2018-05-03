Wykonaj następujące kroki, aby zainstalować i uruchomić bazy danych MongoDB na maszynie wirtualnej z systemem Windows Server.

> [!IMPORTANT]
> Funkcje zabezpieczeń bazy danych MongoDB, takich jak uwierzytelnianie i powiązanie adresu IP, nie są domyślnie włączone. Funkcje zabezpieczeń powinny być włączone przed wdrożeniem w środowisku produkcyjnym bazy danych MongoDB.  Aby uzyskać więcej informacji, zobacz [zabezpieczeń i uwierzytelniania](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu pulpitu zdalnego, Otwórz program Internet Explorer z **Start** menu na maszynie wirtualnej.
2. Wybierz **narzędzia** przycisk w prawym górnym rogu.  W **Opcje internetowe**, wybierz pozycję **zabezpieczeń** , a następnie wybierz **Zaufane witryny** ikonę, a na koniec kliknij **witryny** przycisku. Dodaj *https://\*. mongodb.org* do listy zaufanych witryn.
3. Przejdź do [pobiera — bazy danych MongoDB](https://www.mongodb.com/download-center#community).
4. Znajdź **bieżącej wersji stabilnej** z **Community Server**, wybierz najnowszą **64-bitowych** wersji w kolumnie systemu Windows. Pobierz, a następnie uruchom Instalatora MSI.
5. Bazy danych MongoDB zazwyczaj jest zainstalowany w C:\Program Files\MongoDB. Wyszukaj zmiennych środowiskowych na pulpicie, a następnie dodaj ścieżkę danych binarnych bazy danych MongoDB do zmiennej PATH. Na przykład może znaleźć plików binarnych w C:\Program Files\MongoDB\Server\3.4\bin na tym komputerze.
6. Tworzenie katalogów danych i dziennika bazy danych MongoDB dysku danych (takich jak dysk **F:**) utworzone w poprzednich krokach. Z **Start**, wybierz pozycję **wiersza polecenia** aby otworzyć okno wiersza polecenia.  Wpisz:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Aby uruchomić bazy danych, uruchom polecenie:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Wszystkie komunikaty dziennika są kierowane do *F:\MongoLogs\mongolog.log* plików serwera mongod.exe rozpoczęciu i preallocates plików dziennika. Może upłynąć kilka minut dla bazy danych MongoDB do przydzielenia plików dziennika i rozpocząć nasłuchiwania dla połączenia. Wiersz polecenia pozostaje koncentruje się na to zadanie jest uruchomiona wystąpienia bazy danych MongoDB.
8. Aby uruchomić powłoki administracyjne bazy danych MongoDB, otwiera inne okno polecenia z **Start** i wpisz następujące polecenia:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    Baza danych została utworzona przez insert.
9. Alternatywnie możesz zainstalować mongod.exe jako usługa:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    Zainstalowano usługę o nazwie bazy danych MongoDB z opisem "BD o Mongo". `--logpath` Opcji należy używać, aby określić plik dziennika, ponieważ uruchomiona usługa ma okno polecenia, aby wyświetlić dane wyjściowe.  `--logappend` Opcja określa, czy ponowne uruchomienie usługi powoduje, że dane wyjściowe do dołączenia do istniejącego pliku dziennika.  `--dbpath` Opcji określa lokalizację katalogu danych. Aby bardziej związane z usługą opcji wiersza polecenia, zobacz [opcje wiersza polecenia związane z usługą][MongoWindowsSvcOptions].

    Aby uruchomić usługę, uruchom następujące polecenie:

        C:\> net start MongoDB
10. Bazy danych MongoDB jest zainstalowana i uruchomiona, należy otworzyć port w Zaporze systemu Windows, więc możesz zdalnie nawiązać połączenie bazy danych MongoDB.  Z **Start** menu, wybierz opcję **narzędzia administracyjne** , a następnie **Zapora systemu Windows z zabezpieczeniami zaawansowanymi**.
11. () w okienku po lewej stronie wybierz **reguły ruchu przychodzącego**.  W **akcje** okienko po prawej stronie wybierz **nową regułę...** .

    ![Zapora systemu Windows][Image1]

    (b) w **Kreatora nowej reguły przychodzącej**, wybierz pozycję **portu** , a następnie kliknij przycisk **dalej**.

    ![Zapora systemu Windows][Image2]

    c) wybierz **TCP** , a następnie **określone porty lokalne**.  Określ port "27017" (domyślny port bazy danych MongoDB nasłuchuje), a następnie kliknij przycisk **dalej**.

    ![Zapora systemu Windows][Image3]

    d) wybierz **zezwalały na połączenie** i kliknij przycisk **dalej**.

    ![Zapora systemu Windows][Image4]

    e) kliknij **dalej** ponownie.

    ![Zapora systemu Windows][Image5]

    f) określ nazwę reguły, takie jak "MongoPort", a następnie kliknij przycisk **Zakończ**.

    ![Zapora systemu Windows][Image6]

12. Jeśli punkt końcowy nie skonfigurowała bazy danych mongodb, podczas tworzenia maszyny wirtualnej, możesz to zrobić teraz. Należy zarówno reguły zapory, jak i punktu końcowego, aby mieć możliwość nawiązania połączenia do bazy danych MongoDB.

  W portalu Azure kliknij **maszyn wirtualnych (klasyczne)**, kliknij nazwę nowej maszyny wirtualnej, a następnie kliknij przycisk **punkty końcowe**.

    ![Punkty końcowe][Image7]

13. Kliknij pozycję **Dodaj**.

14. Dodawanie punktu końcowego o nazwie "Mongo" protokołu **TCP**, a oba **publicznego** i **prywatnej** porty ustawioną wartość "27017". Otwarcie tego portu umożliwia bazy danych MongoDB ma być dostępna zdalnie.

    ![Punkty końcowe][Image9]

> [!NOTE]
> Port 27017 jest domyślny port używany przez bazy danych MongoDB. Ten port domyślny można zmienić, określając `--port` parametru podczas uruchamiania serwera mongod.exe. Upewnij się, że ten sam numer portu w zaporze i punktu końcowego "Mongo" w poprzednich instrukcji.
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
