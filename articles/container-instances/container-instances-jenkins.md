---
title: Użyj wystąpień kontenera Azure jako Wpięć agenta kompilacji
description: Dowiedz się, jak używać wystąpień kontenera platformy Azure, zgodnie z Wpięć agenta kompilacji.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: 4df230c8306a3876e94a5e9ada5e7408f134ba26
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359549"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Użyj wystąpień kontenera Azure jako Wpięć agenta kompilacji

Wystąpień kontenera platformy Azure (ACI) zapewnia na żądanie, burstable i izolowane środowisko uruchamiania konteneryzowanych obciążeń. Z powodu tych atrybutów ACI sprawia, że bardzo platformą dla zadania kompilacji Wpięć na dużą skalę. W tym artykule przedstawiono wdrażania i przy użyciu serwera Wpięć, wstępnie skonfigurowanego z ACI jako element docelowy kompilacji.

Aby uzyskać więcej informacji na wystąpień kontenera platformy Azure, zobacz [o wystąpień kontenera Azure][about-aci].

## <a name="deploy-a-jenkins-server"></a>Wdrażanie serwera Wpięć

1. W portalu Azure wybierz **Utwórz zasób** i wyszukaj **Wpięć**. Wybierz oferty Wpięć z wydawcą z **Microsoft**, a następnie wybierz **Utwórz**.

2. Wprowadź następujące informacje **podstawy** formularza, a następnie wybierz **OK**.

   - **Nazwa**: Wprowadź nazwę dla wdrożenia Wpięć.
   - **Nazwa użytkownika**: Wprowadź nazwę użytkownika dla administratora Wpięć maszyny wirtualnej.
   - **Typ uwierzytelniania**: Firma Microsoft zaleca klucz publiczny SSH do uwierzytelniania. Jeśli wybierzesz tę opcję, Wklej klucz publiczny SSH używanego do logowania do maszyny wirtualnej Wpięć.
   - **Subskrypcja**: wybierz subskrypcję platformy Azure.
   - **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.
   - **Lokalizacja**: Wybierz lokalizację na serwerze Wpięć.

   ![Podstawowe ustawienia wdrożenia portalu Wpięć](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Na **dodatkowe ustawienia** tworzą, wykonaj następujące elementy:

   - **Rozmiar**: wybierz opcję odpowiednią rozmiaru Wpięć maszyny wirtualnej.
   - **Typ dysku maszyny Wirtualnej**: Określ **HDD** (dysk twardy) lub **SSD** (SSD) dla serwera Wpięć.
   - **Sieć wirtualna**: Wybierz strzałkę, aby zmodyfikować ustawienia domyślne.
   - **Podsieci**: Wybierz strzałkę, sprawdź informacje, a następnie wybierz **OK**.
   - **Publiczny adres IP**: Wybierz strzałkę, aby zapewnić niestandardową nazwę publicznego adresu IP, skonfigurowania jednostki SKU i metoda przydziału.
   - **Etykieta nazwy domeny**: Określ wartość do utworzenia w pełni kwalifikowany adres URL do Wpięć maszyny wirtualnej.
   - **Typ zlecenia Wpięć**: Wybierz typ żądanej wersji z opcji: **LTS**, **co tydzień kompilacji**, lub **zweryfikować Azure**.

   ![Dodatkowe ustawienia wdrożenia portalu Wpięć](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Usługi integracji podmiotu zabezpieczeń, wybierz **Auto(MSI)** mają [tożsamości zarządzanych usługi Azure] [ managed-service-identity] automatycznie Utwórz tożsamość uwierzytelniania dla Wpięć wystąpienie. Wybierz **ręcznego** zapewnienie własne poświadczenia główne usługi.

5. Agenci chmurze skonfiguruj oparte na chmurze platforma dla zadania kompilacji Wpięć. Dla tego artykułu, wybierz **ACI**. W agencie chmury ACI każde zadanie kompilacji Wpięć jest uruchamiane w wystąpieniu kontenera.

   ![Ustawienia integracji chmury Wpięć wdrożenia portalu](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Po wykonaniu tych ustawień integracji, wybierz **OK**, a następnie wybierz **OK** ponownie na podsumowania weryfikacji. Wybierz **Utwórz** na **warunki użytkowania** podsumowania. Serwer Wpięć zajmuje kilka minut, aby wdrożyć.

## <a name="configure-jenkins"></a>Konfigurowanie usługi Jenkins

1. W portalu Azure przejdź do grupy zasobów Wpięć, wybierz maszynę wirtualną, Wpięć i zanotuj nazwę DNS.

   ![Nazwa DNS w szczegóły Wpięć maszyny wirtualnej](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Wybierz nazwę DNS maszyny wirtualnej Wpięć i skopiuj zwracany ciąg SSH.

   ![Instrukcje logowania Wpięć z ciągiem SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Otwórz sesję terminala w systemie deweloperskim i Wklej w ciągu SSH w ostatnim kroku. Aktualizacja `username` określony podczas wdrażania serwera Wpięć nazwy użytkownika.

4. Po sesji jest połączony, uruchom następujące polecenie, aby pobrać hasło początkowej administratora:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Pozostaw sesji SSH i tunelu uruchomiona, a następnie przejdź do http://localhost:8080 w przeglądarce. Wklej hasło administratora początkowa w polu, a następnie wybierz **Kontynuuj**.

   ![Ekran "Odblokować Wpięć" z polem dla hasła administratora](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Wybierz **Instalowanie wtyczki sugerowane** można zainstalować wszystkie zalecane Wpięć wtyczek.

   ![Ekran "Dostosować Wpięć" z "Instalowanie wtyczki sugerowane" wybrane](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Tworzenie konta administratora. To konto jest używane do logowania do i pracy z wystąpieniem Wpięć.

   ![Ekran "Utwórz pierwszy Administrator", przy użyciu poświadczeń wypełnione](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Wybierz **zapisać i zakończenia**, a następnie wybierz **Rozpoczynanie korzystania z Wpięć** w celu ukończenia konfiguracji.

Wpięć jest teraz skonfigurowane i gotowe do tworzenia i wdrażania kodu. Na przykład prostą aplikację Java służy do pokazują kompilacji Wpięć na wystąpień kontenera platformy Azure.

## <a name="create-a-build-job"></a>Tworzenie zadania kompilacji

Podczas korzystania z obrazem kontenera jako Wpięć kompilacji docelowej, należy określić obraz, który zawiera wszystkie narzędzia niezbędne do pomyślnego utworzenia kompilacji. Aby określić obrazu:

1. Wybierz **Zarządzanie Wpięć** > **skonfigurować System** i przewiń w dół do **chmury** sekcji. Na przykład zaktualizować wartość obrazu Docker **microsoft/java na azure wpięć podrzędna**.

   Gdy wszystko będzie gotowe, wybierz **zapisać** aby wrócić do pulpitu nawigacyjnego Wpięć.

   ![Konfiguracja chmury Wpięć](./media/container-instances-jenkins/jenkins-aci-image.png)

2. Teraz utworzyć Wpięć zadania kompilacji. Wybierz **nowy element**, nadaj nazwę projektu kompilacji takich jak **aci-java pokaz**, wybierz pozycję **stylu projektu**i wybierz **OK**.

   ![Pole nazwy zadania kompilacji, oraz listę typów projektów](./media/container-instances-jenkins/jenkins-new-job.png)

3. W obszarze **ogólne**, upewnij się, że **Ogranicz, w którym można uruchomić tego projektu** jest zaznaczone. Wprowadź **linux** dla wyrażenia etykiety. Taka konfiguracja powoduje, że to zadanie kompilacji zostanie uruchomiona w chmurze ACI.

   ![Kartę "Ogólne" szczegóły konfiguracji](./media/container-instances-jenkins/jenkins-job-01.png)

4. W obszarze **zarządzania kodem źródłowym**, wybierz pozycję **Git** , a następnie wprowadź **https://github.com/spring-projects/spring-petclinic.git** dla adresu URL repozytorium. To repozytorium GitHub zawiera przykładowy kod aplikacji.

   ![Karta "Zarządzania kod źródłowy" z informacji dotyczących kodu źródłowego](./media/container-instances-jenkins/jenkins-job-02.png)

5. W obszarze **kompilacji**, wybierz pozycję **kroku kompilacji Dodaj** i wybierz **wywołania najwyższego poziomu celów Maven**. Wprowadź **pakietu** jako cel kroku kompilacji.

   ![Karta "Kompilacji", z opcjami kroku kompilacji](./media/container-instances-jenkins/jenkins-job-03.png)

6. Wybierz pozycję **Zapisz**.

## <a name="run-the-build-job"></a>Uruchom zadanie kompilacji

Aby przetestować zadania kompilacji i obserwować wystąpień kontenera Azure jako platforma kompilacji, należy ręcznie uruchomić kompilację.

1. Wybierz **kompilacji teraz** można rozpocząć zadania kompilacji. Trwa kilka minut, aż zadanie można uruchomić. Powinien zostać wyświetlony stan, która jest podobna do poniższej ilustracji:

   !["Historia kompilacji" informacje o stanie zadania](./media/container-instances-jenkins/jenkins-job-status.png)

2. Gdy zadanie jest uruchomione, otwórz Azure portal i przyjrzyj się Wpięć grupy zasobów. Powinna zostać wyświetlona utworzono wystąpienie kontenera. Zadanie Wpięć jest uruchomione w tym wystąpieniu.

   ![Kontener wystąpienie w grupie zasobów](./media/container-instances-jenkins/jenkins-aci.png)

3. Zgodnie z Wpięć uruchamia zadania więcej niż skonfigurowana liczba modułów Wpięć (domyślnie: 2), są tworzone wielu wystąpień kontenera.

   ![Nowo utworzona wystąpień kontenera](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po zakończeniu wszystkich zadań kompilacji, są usuwane wystąpień kontenera.

   ![Grupy zasobów z wystąpień kontenera usunięte](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat Wpięć na platformie Azure, zobacz [Azure i Wpięć][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md