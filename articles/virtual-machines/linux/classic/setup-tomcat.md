---
title: Konfigurowanie oprogramowania Apache Tomcat na maszynie wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić tomcat7 Apache przy użyciu usługi Azure Virtual Machines z systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: 5a5d052052be447ea2ccbd9231d3b03d38c7615c
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266947"
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Konfigurowanie serwera Tomcat7 na maszynie wirtualnej systemu Linux przy użyciu platformy Azure
Apache Tomcat (lub po prostu serwer Tomcat będący również nazywanych Tomcat Dżakarta) to serwer sieci web typu open source i kontener serwletów opracowane przez Apache Software Foundation (ASF). Tomcat implementuje Java Servlet i JavaServer Pages (JSP) specyfikacji firmy Sun Microsystems. Tomcat zapewnia czyste Java HTTP środowisku serwera sieci web do uruchamiania kodu języka Java. W najprostszej konfiguracji Tomcat działa w procesie jednego systemu operacyjnego. Ten proces jest uruchamiany maszyny wirtualnej Java (JVM). Każde żądanie HTTP z przeglądarki do serwera Tomcat są przetwarzane jako osobne wątek w procesie serwera Tomcat.  

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Usługa Azure Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule opisano sposób korzystania z klasycznego modelu wdrażania. Zaleca się, że większości nowych wdrożeń korzystać z modelu usługi Resource Manager. Aby użyć szablonu usługi Resource Manager, aby wdrożyć maszynę Wirtualną Ubuntu, z Open JDK i Tomcat, zobacz [w tym artykule](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym artykule spowoduje zainstalowanie serwera Tomcat7 na obrazie systemu Linux i wdrożyć ją na platformie Azure.  

Dowiesz się:  

* Jak utworzyć maszynę wirtualną na platformie Azure.
* Jak przygotować maszynę wirtualną do serwera Tomcat7.
* Jak zainstalować Tomcat7.

Zakłada się, że masz już subskrypcję platformy Azure.  Jeśli nie, możesz zasubskrybować bezpłatnej wersji próbnej w [witrynie internetowej platformy Azure](https://azure.microsoft.com/). Jeśli masz subskrypcję MSDN, zobacz [specjalnych cen Microsoft Azure: MSDN, MPN i BizSpark korzyści](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Aby dowiedzieć się więcej o platformie Azure, zobacz [co to jest Azure?](https://azure.microsoft.com/overview/what-is-azure/).

W tym artykule założono, że masz podstawową wiedzę na temat pracy Tomcat i Linux.  

## <a name="phase-1-create-an-image"></a>Faza 1: Tworzenie obrazu
W tym kroku utworzysz maszynę wirtualną przy użyciu obrazu systemu Linux na platformie Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Krok 1: Generowanie klucza uwierzytelniania SSH
Protokół SSH jest ważnym narzędziem dla administratorów systemów. Konfigurowanie zabezpieczeń dostępu na podstawie hasła określane przez człowieka nie jest jednak zalecane. Złośliwi użytkownicy można podzielić na podstawie nazwy użytkownika i hasło słabe systemu.

Dobra wiadomość jest sposób zamykaj dostępu zdalnego i nie martw się o hasłach. Ta metoda składa się z uwierzytelniania za pomocą asymetrycznych kryptografii. Klucz prywatny użytkownika jest ten, który przyznaje uwierzytelniania. Można nawet zablokować konto użytkownika nie zezwalać na uwierzytelnianie przy użyciu hasła.

Inną zaletą tej metody jest niepotrzebne różne hasła do logowania się na różnych serwerach. Możesz uwierzytelniać się przy użyciu osobistych klucza prywatnego na wszystkich serwerach, co zapobiega konieczności pamiętać kilka haseł.



Wykonaj następujące kroki, aby wygenerować klucz uwierzytelniania SSH.

1. Pobierz i zainstaluj program PuTTYgen z następującej lokalizacji: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Uruchom Puttygen.exe.
3. Kliknij przycisk **Generuj** do generowania kluczy. W procesie można zwiększyć losowości, przesuwając wskaźnik myszy nad pusty obszar w oknie.  
   ![PuTTY zrzut ekranu Generator kluczy, który przedstawia przycisk Generuj nowych kluczy][1]
4. Po zakończeniu procesu Generuj Puttygen.exe zostaną wyświetlone nowy klucz publiczny.  
   ![PuTTY zrzut ekranu Generator kluczy, który zawiera nowy klucz publiczny i Zapisz przycisk klucza prywatnego][2]
5. Zaznacz i skopiuj klucz publiczny i zapisz go w pliku o nazwie publicKey.pem. Nie klikaj pozycji **Zapisz klucz publiczny**, ponieważ format pliku zapisanego klucz publiczny jest inny niż klucz publiczny, chcemy.
6. Kliknij przycisk **Zapisz klucz prywatny**i zapisz go w pliku o nazwie privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Krok 2: Tworzenie obrazu w witrynie Azure portal
1. W [portal](https://portal.azure.com/), kliknij przycisk **Utwórz zasób** na pasku zadań, aby utworzyć obraz. Następnie wybierz obraz systemu Linux opiera się na Twoje potrzeby. W poniższym przykładzie użyto obrazu Ubuntu 14.04.
![Zrzut ekranu przedstawiający przycisk Nowy portal][3]

2. Aby uzyskać **nazwy hosta**, określ nazwę dla adresu URL, do którego należy, jak i klientów internetowych będą uzyskać dostęp do tej maszyny wirtualnej. Zdefiniuj ostatnią część nazwy DNS, na przykład tomcatdemo. Azure następnie generuje adres URL jako tomcatdemo.cloudapp.net.  

3. Aby uzyskać **klucz uwierzytelniania SSH**, skopiuj wartość klucza z pliku publicKey.pem, który zawiera klucz publiczny, generowane przez program PuTTYgen.  
![Pole klucza uwierzytelniania SSH w portalu][4]

4. Skonfiguruj inne ustawienia zgodnie z potrzebami, a następnie kliknij przycisk **Utwórz**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Faza 2: Przygotowywanie maszyny wirtualnej dla serwera Tomcat7
W tej fazie zostanie konfigurowania punktu końcowego dla serwera Tomcat ruchu, a następnie nawiązać połączenie z nową maszyną wirtualną.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Krok 1: Otwórz port HTTP, aby zezwolić na dostęp w sieci web
Punkty końcowe na platformie Azure składa się z protokołu TCP lub UDP, wraz z portem publicznych i prywatnych. Port prywatny jest numer portu, którego usługa nasłuchuje na maszynie wirtualnej. Port publiczny jest port nasłuchujący usłudze w chmurze Azure zewnętrznie dla ruchu przychodzącego, oparty na Internecie.  

TCP port 8080 jest domyślny numer portu używanego do nasłuchiwania serwera Tomcat. Ten port jest otwarty z punktem końcowym usługi platformy Azure, możesz i innych klientów internetowych mają dostęp do strony serwera Tomcat.  

1. W portalu, kliknij przycisk **Przeglądaj** > **maszyn wirtualnych**, a następnie kliknij maszynę wirtualną, która została utworzona.  
   ![Zrzut ekranu przedstawiający katalogu maszyn wirtualnych][5]
2. Aby dodać punkt końcowy do maszyny wirtualnej, kliknij **punktów końcowych** pole.
   ![Zrzut ekranu przedstawiający okno punktów końcowych][6]
3. Kliknij pozycję **Add** (Dodaj).  

   1. Dla punktu końcowego, wprowadź nazwę punktu końcowego w **punktu końcowego**, a następnie wprowadź 80 w **Port publiczny**.  

      Jeśli zostanie ustawiona na 80, nie ma potrzeby podaj numer portu w adresie URL, który umożliwia dostęp do serwera Tomcat. Na przykład http://tomcatdemo.cloudapp.net.    

      Jeśli zostanie ustawiona na inną wartość, takich jak 81, musisz dodać numer portu do adresu URL do dostępu do serwera Tomcat. Przykład: http://tomcatdemo.cloudapp.net:81/.
   2. Wprowadź 8080 w **Port prywatny**. Domyślnie Tomcat nasłuchuje na porcie TCP 8080. Jeśli zmienisz domyślny nasłuchiwać portu Tomcat, należy zaktualizować **Port prywatny** być taka sama jak Tomcat port nasłuchujący.  
      ![Zrzut ekranu z interfejsu użytkownika, który zawiera polecenie Dodaj, Port publiczny i Port prywatny][7]
4. Kliknij przycisk **OK** można dodać punkt końcowy do maszyny wirtualnej.

### <a name="step-2-connect-to-the-image-you-created"></a>Krok 2: Połączyć się z obrazem, który został utworzony
Można wybrać dowolne narzędzie SSH, aby nawiązać połączenie z maszyną wirtualną. W tym przykładzie używamy programu PuTTY.  

1. Pobierz nazwę DNS maszyny wirtualnej z portalu.
    1. Kliknij przycisk **Przeglądaj** > **maszyn wirtualnych**.
    2. Wybierz nazwę maszyny wirtualnej, a następnie kliknij przycisk **właściwości**.
    3. W **właściwości** kafelków, Szukaj w **nazwy domeny** pole, aby uzyskać nazwę DNS.  

2. Pobierz numer portu dla połączenia SSH z **SSH** pole.  
![Zrzut ekranu pokazujący numeru portu połączenia SSH][8]

3. Pobierz [PuTTY](http://www.putty.org/).  

4. Po pobraniu, kliknij plik wykonywalny Putty.exe. W konfiguracji programu PuTTY skonfiguruj opcje podstawowego z nazwą hosta i port numer, który jest uzyskiwana z właściwości maszyny wirtualnej.   
![Zrzut ekranu przedstawiający opcje nazwy i portu hosta Konfiguracja programu PuTTY][9]

5. W okienku po lewej stronie kliknij **połączenia** > **SSH** > **uwierzytelniania**, a następnie kliknij przycisk **Przeglądaj** do określenia Lokalizacja pliku privateKey.ppk. Plik privateKey.ppk zawiera klucz prywatny, który jest generowany przez program PuTTYgen wcześniej w "faza 1: Tworzenie obrazu"dalszej części tego artykułu.  
![Zrzut ekranu przedstawiający przycisk przeglądania i hierarchii katalogów połączenia][10]

6. Kliknij przycisk **Open** (Otwórz). Użytkownik może otrzymywać alerty, okno komunikatu. Jeśli skonfigurowano nazwę DNS i numer portu poprawnie, kliknij **tak**.
![Zrzut ekranu przedstawiający powiadomienie][11]

7. Monit o podanie nazwy użytkownika.  
![Zrzut ekranu przedstawiający miejsce wprowadzania nazwy użytkownika][12]

8. Wprowadź nazwę użytkownika, który został użyty do utworzenia maszyny wirtualnej w "faza 1: Tworzenie obrazu"sekcji we wcześniejszej części tego artykułu. Zostanie wyświetlony podobny do poniższego:  
![Zrzut ekranu pokazujący potwierdzenie uwierzytelniania][13]

## <a name="phase-3-install-software"></a>Faza 3: Instalowanie oprogramowania
Na tym etapie należy zainstalować środowisko uruchomieniowe Java, Tomcat7 i inne składniki serwera Tomcat7.  

### <a name="java-runtime-environment"></a>Środowisko wykonawcze języka Java
Tomcat został napisany w języku Java. Zobacz [Azure obsługiwane JDK](https://aka.ms/azure-jdks) informacji na temat uzyskiwania w pełni obsługiwane środowiska uruchomieniowe Java. Możesz również skorzystać z własnych, ale w dalszej części tego artykułu, będą używać wersji obsługiwanej przez platformę Azure.


#### <a name="install-azure-supported-jdk"></a>Instalowanie platformy Azure, obsługiwany zestaw JDK

Postępuj zgodnie z `apt-get` udokumentowane w instrukcji dotyczących instalacji [Azul Zulu Enterprise na platformie Azure](https://www.azul.com/downloads/azure-only/zulu/#apt-repo) witryny sieci Web.

#### <a name="confirm-that-java-installation-is-successful"></a>Upewnij się, że instalacja języka Java zakończyła się powodzeniem
Aby sprawdzić, czy środowisko wykonawcze języka Java jest poprawnie zainstalowane, można użyć polecenia podobnego do poniższego:  
    Java — w wersji  

Powinien zostać wyświetlony komunikat, jak pokazano poniżej: ![Pomyślne komunikat instalacji OpenJDK][14]


### <a name="install-tomcat7"></a>Instalowanie serwera Tomcat7
Użyj następującego polecenia do zainstalowania serwera Tomcat7.  

    sudo apt-get install tomcat7  

Jeśli nie używasz serwera Tomcat7, należy użyć odpowiednią odmianę tego polecenia.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Upewnij się, że Tomcat7 Instalacja powiodła się
Aby sprawdzić, czy pomyślnie zainstalowano Tomcat7, przejdź do nazwy DNS serwera Tomcat. W tym artykule jest przykładowy adres URL http://tomcatexample.cloudapp.net/. Jeśli zobaczysz następujący komunikat, Tomcat7 jest poprawnie zainstalowany.
![Pomyślne komunikat instalacji serwera Tomcat7][16]

### <a name="install-other-tomcat7-components"></a>Zainstaluj inne składniki serwera Tomcat7
Istnieją inne opcjonalne składniki Tomcat, którzy mogą instalować.  

Użyj **tomcat7 wyszukiwania polecenia apt-cache "sudo"** polecenie, aby wyświetlić wszystkie dostępne składniki. Użyj następujących poleceń do instalacji niektórych składników przydatne.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Faza 4: Konfigurowanie serwera Tomcat7
Na tym etapie administrowania Tomcat.

### <a name="start-and-stop-tomcat7"></a>Uruchamianie i zatrzymywanie serwera Tomcat7
Serwera Tomcat7 uruchamia się automatycznie, gdy należy ją zainstalować. Można również uruchomić za pomocą następującego polecenia:   

    sudo /etc/init.d/tomcat7 start

To stop Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Aby wyświetlić stan Tomcat7:

    sudo /etc/init.d/tomcat7 status

Aby ponownie uruchomić usługi serwera Tomcat: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Administracja Tomcat7
Można edytować plik konfiguracyjny użytkownika Tomcat, aby skonfigurować swoje poświadczenia administratora. Użyj następującego polecenia:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Oto przykład:  
![Zrzut ekranu przedstawiający dane wyjściowe z polecenia "sudo" vi][17]  

> [!NOTE]
> Utwórz silne hasło dla nazwy użytkownika administratora.  

Po wprowadzeniu zmian w tym pliku, należy ponownie uruchomić usługi serwera Tomcat7 za pomocą następującego polecenia, aby upewnić się, że zmiany zostały wprowadzone:  

    sudo /etc/init.d/tomcat7 restart  

Otwórz przeglądarkę i wprowadź **http://<your tomcat server DNS name>/Menedżer/html** jako adres URL. Na przykład w tym artykule, adres URL jest http://tomcatexample.cloudapp.net/manager/html.  

Po nawiązaniu połączenia powinny zostać wyświetlone informacje podobne do następujących:  
![Zrzut ekranu Menedżera aplikacji sieci Web Tomcat][18]

## <a name="common-issues"></a>Typowe problemy
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Nie można uzyskać dostępu maszynę wirtualną z Tomcat i w Moodle z Internetu
#### <a name="symptom"></a>Objaw  
  Tomcat jest uruchomiony, ale nie można wyświetlić domyślną stronę Tomcat, za pomocą przeglądarki.
#### <a name="possible-root-cause"></a>Możliwe przyczyny   

  * Port nasłuchiwania serwera Tomcat nie jest taki sam jak port prywatny punktu końcowego maszyny wirtualnej, dla serwera Tomcat ruchu.  

     Sprawdź port publiczny i port prywatny ustawienia punktu końcowego i upewnij się, że port prywatny jest taka sama jak Tomcat port nasłuchujący. Zobacz "faza 1: Tworzenie obrazu"sekcji tego artykułu, aby uzyskać instrukcje na temat konfigurowania punktów końcowych dla maszyny wirtualnej.  

     Aby określić numer portu nasłuchiwania serwera Tomcat, otwórz /etc/httpd/conf/httpd.conf (wersja firmy Red Hat) lub /etc/tomcat7/server.xml (wersja Debian). Domyślnie portu nasłuchiwania serwera Tomcat to 8080. Oto przykład:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Jeśli używasz maszyny wirtualnej, takie jak Debian lub Ubuntu, jeśli chcesz zmienić domyślny port programu Tomcat nasłuchiwania (na przykład 8081), możesz również otworzyć port dla systemu operacyjnego. Najpierw otwórz profilu:  

        sudo vi /etc/default/tomcat7  

     Następnie usuń komentarz z ostatniego wiersza i zmień "no" na "tak".  

        AUTHBIND=yes
  2. Zapora została wyłączona portu nasłuchiwania serwera Tomcat.

     Widać tylko Tomcat domyślną stronę z hosta lokalnego. Problem polega na najbardziej prawdopodobne, że port, który jest posłuchaliśmy przez Tomcat jest blokowany przez zaporę. Narzędzie w3m przejdź na stronę sieci Web. Następujące polecenia Zainstaluj w3m i przejdź do strony domyślnej Tomcat:  


        "sudo" yum install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Rozwiązanie

  * Jeśli nasłuchiwania serwera Tomcat port nie jest taki sam jak port prywatny punktu końcowego dla ruchu sieciowego do maszyny wirtualnej, należy zmienić port prywatny jest taka sama jak Tomcat port nasłuchujący.   
  2. Jeśli ten problem jest spowodowany przez zaporę/iptables, należy dodać następujące wiersze do /etc/sysconfig/iptables. Drugi wiersz jest wymagana tylko dla ruchu https:  

      -A -p tcp -m tcp dport — 80 -j AKCEPTUJ wejściowych

      -A dane wejściowe -p tcp -m tcp — dport 443 -j ACCEPT  

     > [!IMPORTANT]
     > Upewnij się, poprzednie wiersze są umieszczone powyżej, które globalnie może ograniczyć dostęp, podobny do następującego: - wejściowych -j Odrzuć — Odrzuć — za pomocą zabronione hosta protokołu icmp



Aby ponownie załadować iptables, uruchom następujące polecenie:

    service iptables restart

Zostało to przetestowane w CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Odmowa podczas przekazywania plików projektu do /var/lib/tomcat7/webapps /
#### <a name="symptom"></a>Objaw
  W przypadku użycia klienta protokołu SFTP (np. programu FileZilla), aby nawiązać połączenie z maszyną wirtualną, a następnie przejdź do /var/lib/tomcat7/webapps/opublikowanie witryny, otrzymasz komunikat o błędzie podobny do następującego:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Możliwe przyczyny
  Nie masz uprawnień dostępu do folderu /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Rozwiązanie  
  Należy uzyskać uprawnienia z konta głównego. Możesz zmienić własności tego folderu w katalogu głównym na nazwę użytkownika, którego użyto podczas aprowizacji maszyny. Oto przykład z azureuser nazwa konta:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Użyj opcji -R, aby zastosować uprawnienia dla wszystkich plików w katalogu za.  

  To polecenie działa również w przypadku katalogów. Opcja -R zmienia uprawnienia dla wszystkich plików i katalogów w katalogu. Oto przykład:  

     sudo chown -R username:group directory  

  To polecenie powoduje zmianę własności (użytkowników i grup) dla wszystkich plików i katalogów, które znajdują się w katalogu.  

  Następujące polecenie zmienia tylko uprawnienia katalogu folderu. Pliki i foldery w katalogu nie są zmieniane.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
