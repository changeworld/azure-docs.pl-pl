Wykonaj następujące kroki, aby zainstalować i uruchomić bazy danych MongoDB na maszynie wirtualnej z systemem Windows Server.

> [!IMPORTANT]
> Funkcje zabezpieczeń bazy danych MongoDB, takie jak uwierzytelnianie i powiązanie adresu IP, nie są domyślnie włączone. Funkcje zabezpieczeń powinien być włączony przed wdrożeniem usługi MongoDB w środowisku produkcyjnym.  Aby uzyskać więcej informacji, zobacz [zabezpieczeń i uwierzytelniania](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu pulpitu zdalnego, Otwórz program Internet Explorer z **Start** menu na maszynie wirtualnej.
2. Wybierz **narzędzia** przycisk w prawym górnym rogu.  W **Opcje internetowe**, wybierz opcję **zabezpieczeń** , a następnie wybierz pozycję **Zaufane witryny** ikonę, a na koniec kliknij **witryn** przycisku. Dodaj *https://\*. mongodb.org* do listy zaufanych witryn.
3. Przejdź do [pliki do pobrania — bazy danych MongoDB](https://www.mongodb.com/download-center#community).
4. Znajdź **bieżącą stabilną wersję** z **Community Server**, wybierz najnowszy **64-bitowych** wersji w kolumnie Windows. Pobierz, a następnie uruchom Instalatora MSI.
5. Bazy danych MongoDB jest zwykle instalowany w C:\Program Files\MongoDB. Wyszukaj zmienne środowiskowe na pulpicie, a następnie dodać ścieżkę do plików binarnych bazy danych MongoDB do zmiennej PATH. Na przykład pliki binarne może się okazać na C:\Program Files\MongoDB\Server\3.4\bin na swojej maszynie.
6. Tworzenie katalogów danych i dziennika bazy danych MongoDB na dysku danych (np. dysku **F:**) został utworzony w poprzednich krokach. Z **Start**, wybierz opcję **polecenia** aby otworzyć okno wiersza polecenia.  Wpisz:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Aby uruchomić bazy danych, uruchom polecenie:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Wszystkie komunikaty dziennika są kierowane do *F:\MongoLogs\mongolog.log* plików serwera mongod.exe rozpoczyna się i preallocates plików dziennika. Może upłynąć kilka minut, zanim bazy danych MongoDB do przydzielenia plików dziennika i rozpocząć nasłuchiwania połączeń. Wiersz polecenia pozostaje koncentruje się na to zadanie, gdy wystąpienie bazy danych MongoDB jest uruchomiona.
8. Aby uruchomić administracyjne powłoki bazy danych MongoDB, Otwórz inne okno polecenia z **Start** i wpisz następujące polecenia:

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

    Baza danych jest tworzony przez insert.
9. Alternatywnie można zainstalować mongod.exe jako usługi:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    Zainstalowano usługę o nazwie bazy danych MongoDB z opisem "Mongo DB". `--logpath` Opcji należy używać, aby określić plik dziennika, ponieważ uruchomionej usługi nie ma okno polecenia, aby wyświetlić dane wyjściowe.  `--logappend` Opcja określa, że ponowne uruchomienie usługi powoduje, że dane wyjściowe dołączyć do istniejącego pliku dziennika.  `--dbpath` Opcja określa lokalizację katalogu danych. Aby bardziej związanych z usługą opcji wiersza polecenia, zobacz [związanych z usługą opcje wiersza polecenia][MongoWindowsSvcOptions].

    Aby uruchomić usługę, uruchom następujące polecenie:

        C:\> net start MongoDB
10. Teraz, MongoDB jest zainstalowana i uruchomiona, należy otworzyć port Zapory Windows, dzięki czemu możesz zdalnie nawiązać połączenie bazy danych MongoDB.  Z **Start** menu, wybierz opcję **narzędzia administracyjne** i następnie **Zapora Windows z zabezpieczeniami zaawansowanymi**.
11. () w okienku po lewej stronie wybierz **reguły ruchu przychodzącego**.  W **akcje** w okienku po prawej stronie wybierz **nową regułę...** .

    ![Zapora Windows][Image1]

    (b) w **Kreatora nowej reguły przychodzącej**, wybierz opcję **portu** a następnie kliknij przycisk **dalej**.

    ![Zapora Windows][Image2]

    c) wybierz **TCP** i następnie **określone porty lokalne**.  Określ port "27017" (domyślny port nasłuchuje bazy danych MongoDB), a następnie kliknij przycisk **dalej**.

    ![Zapora Windows][Image3]

    d) wybierz **Zezwalaj na połączenie** i kliknij przycisk **dalej**.

    ![Zapora Windows][Image4]

    (e) kliknij **dalej** ponownie.

    ![Zapora Windows][Image5]

    f) podaj nazwę reguły, takie jak "MongoPort", a następnie kliknij przycisk **Zakończ**.

    ![Zapora Windows][Image6]

12. Jeśli nie skonfigurujesz punktu końcowego usługi dla bazy danych MongoDB podczas tworzenia maszyny wirtualnej, możesz to zrobić teraz. Należy reguły zapory i punkt końcowy, aby mieć możliwość połączenia z usługą MongoDB.

  W witrynie Azure portal kliknij pozycję **maszyny wirtualne (klasyczne)**, kliknij nazwę swojej nowej maszyny wirtualnej, a następnie kliknij **punktów końcowych**.

    ![Punkty końcowe][Image7]

13. Kliknij pozycję **Add** (Dodaj).

14. Dodawanie punktu końcowego o nazwie "Mongo", protokół **TCP**i wartościami **publicznych** i **prywatnej** portów jest ustawiona na "27017". Otwarcie tego portu umożliwia usługi MongoDB na zdalny dostęp do.

    ![Punkty końcowe][Image9]

> [!NOTE]
> Port 27017 jest domyślnym portem używanym przez bazy danych MongoDB. Ten port domyślny można zmienić, określając `--port` parametru podczas uruchamiania serwera mongod.exe. Upewnij się nadać tego samego numeru portu w zaporze i punktu końcowego "Mongo" w poprzednich instrukcji.
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
