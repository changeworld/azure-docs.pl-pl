---
title: Użyj usługi Azure Container Instances jako Jenkins agenta kompilacji
description: Dowiedz się, jak używać usługi Azure Container Instances, jak agent kompilacji usługi Jenkins.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: 2687a64bfd952888086862d929a3e5869aee5a4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584171"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Użyj usługi Azure Container Instances jako Jenkins agenta kompilacji

Usługa Azure Container Instances (ACI) zapewnia na żądanie, z możliwością zwiększania wydajności i izolowane środowisko do uruchamiania obciążeń konteneryzowanych. Z powodu te atrybuty ACI sprawia, że doskonała platforma do uruchamiania zadań kompilacji usługi Jenkins na dużą skalę. Ten artykuł przeprowadzi wdrażania i korzystania z serwera Jenkins, który jest wstępnie skonfigurowana za pomocą usługi ACI jako obiekt docelowy kompilacji.

Aby uzyskać więcej informacji na temat usługi Azure Container Instances, zobacz [usługi Azure Container Instances][about-aci].

## <a name="deploy-a-jenkins-server"></a>Wdrażanie serwera Jenkins

1. W witrynie Azure portal wybierz **Utwórz zasób** i wyszukaj **Jenkins**. Wybierz ofertę usługi Jenkins, za pomocą Wydawca **Microsoft**, a następnie wybierz pozycję **Utwórz**.

2. Wprowadź następujące informacje **podstawy** formularza, a następnie wybierz **OK**.

   - **Nazwa**: Wprowadź nazwę dla wdrożenia serwera Jenkins.
   - **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora maszyny wirtualnej usługi Jenkins.
   - **Typ uwierzytelniania**: Firma Microsoft zaleca klucza publicznego SSH do uwierzytelniania. Jeśli wybierzesz tę opcję, Wklej klucz publiczny SSH ma być używany do zalogowania się do maszyny wirtualnej usługi Jenkins.
   - **Subskrypcja**: Wybierz subskrypcję platformy Azure.
   - **Grupa zasobów**: Utwórz grupę zasobów lub wybierz istniejącą grupę.
   - **Lokalizacja**: Wybierz lokalizację dla serwera Jenkins.

   ![Podstawowe ustawienia dla wdrażania w portalu narzędzia Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Na **dodatkowe ustawienia** formularza, wykonaj następujące elementy:

   - **Rozmiar**: Wybierz opcję odpowiedniego rozmiaru maszyny wirtualnej usługi Jenkins.
   - **Typ dysku maszyny wirtualnej**: Wybierz opcję **HDD** (dysk twardy) lub **SSD** (SSD) dla serwera Jenkins.
   - **Sieć wirtualna**: Wybierz strzałkę, aby zmodyfikować domyślne ustawienia.
   - **Podsieci**: Wybierz strzałkę, sprawdź informacje, a następnie wybierz **OK**.
   - **Publiczny adres IP**: Wybierz strzałkę, aby podać niestandardową nazwę publicznego adresu IP, konfigurowanie jednostki SKU i set, Metoda przypisania.
   - **Etykieta nazwy domeny**: Określ wartość do utworzenia w pełni kwalifikowany adres URL z maszyną wirtualną serwera Jenkins.
   - **Typ wersji usługi Jenkins**: Wybierz typ żądanej wersji spośród opcji: **LTS**, **co tydzień kompilacji**, lub **zweryfikować Azure**.

   ![Dodatkowe ustawienia wdrażania w portalu narzędzia Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Integracja nazwy głównej usługi, wybierz **Auto(MSI)** mieć [zarządzanych tożsamości dla zasobów platformy Azure] [ managed-identities-azure-resources] automatycznie utworzyć tożsamością uwierzytelniania dla Wystąpienie narzędzia Jenkins. Wybierz **ręczne** zapewnienie własne poświadczenia nazwy głównej usługi.

5. Agentów w chmurze skonfiguruj platformy z opartych na chmurze dla zadań kompilacji usługi Jenkins. Dla tego artykułu, wybierz **ACI**. W agencie chmury ACI każdego zadania kompilacji usługi Jenkins jest uruchamiane w wystąpienia kontenera.

   ![Ustawienia integracji chmury dla wdrażania w portalu narzędzia Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Po zakończeniu korzystania z ustawienia integracji, wybierz **OK**, a następnie wybierz pozycję **OK** ponownie dla podsumowania weryfikacji. Wybierz **Utwórz** na **warunki użytkowania** podsumowania. Serwer Jenkins zajmuje kilka minut, aby wdrożyć.

## <a name="configure-jenkins"></a>Konfigurowanie usługi Jenkins

1. W witrynie Azure portal przejdź do grupy zasobów usługi Jenkins wybierz maszynę wirtualną usługi Jenkins i zwróć uwagę na nazwy DNS.

   ![Nazwa DNS w szczegółach dotyczących maszyny wirtualnej usługi Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Wskaż nazwę DNS maszyny Wirtualnej usługi Jenkins i skopiuj zwracanego ciągu SSH.

   ![Instrukcje logowania narzędzia Jenkins przy użyciu parametrów SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Otwórz sesję terminala w systemie deweloperskim, a następnie wklej parametry SSH w ostatnim kroku. Aktualizacja `username` na nazwę użytkownika, które zostały określone podczas wdrażania serwera Jenkins.

4. Po zakończeniu jest połączony, uruchom następujące polecenie, aby pobrać początkowego hasła administratora:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Pozostaw sesji SSH i tunel uruchomiona, a następnie przejdź do `http://localhost:8080` w przeglądarce. Wklej początkowego hasła administratora w polu, a następnie wybierz **Kontynuuj**.

   ![Ekran "Odblokowywanie usługi Jenkins" z polem o hasło administratora](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Wybierz **instalowanie sugerowanych wtyczek** można zainstalować wszystkie zalecane wtyczek serwera Jenkins.

   ![Ekran "Dostosowywanie Jenkins" wybrane "Instalowanie sugerowanych wtyczek"](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Tworzenie konta użytkownika administracyjnego. To konto jest używane do logowania się do i Praca z wystąpienia usługi Jenkins.

   ![Ekran "Utwórz pierwszego użytkownika administratora", przy użyciu poświadczeń wypełnione](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Wybierz **Zapisz i Zakończ**, a następnie wybierz pozycję **rozpocząć korzystanie z narzędzia Jenkins** aby zakończyć konfigurację.

Jenkins jest teraz skonfigurowane i gotowe do kompilowania i wdrażania kodu. W tym przykładzie prostą aplikację Java jest używana do zademonstrowania kompilację narzędzia Jenkins w usłudze Azure Container Instances.

## <a name="create-a-build-job"></a>Tworzenie zadania kompilacji

Zadania kompilacji usługi Jenkins jest gotowy pokazują, że usługi Jenkins jest oparta na wystąpieniu kontenera platformy Azure.

1. Wybierz **nowy element**, nazwij projekt kompilacji takich jak **aci-demo**, wybierz opcję **projekt Freestyle**i wybierz **OK**.

   ![Pole nazwy zadania kompilacji i listy typów projektów](./media/container-instances-jenkins/jenkins-new-job.png)

2. W obszarze **ogólne**, upewnij się, że **ograniczanie, gdzie można uruchomić tego projektu** jest zaznaczone. Wprowadź **linux** wyrażenia etykiety. Ta konfiguracja gwarantuje, że to zadanie kompilacji jest uruchamiany w chmurze ACI.

   ![Kartę "Ogólne" ze szczegółami konfiguracji](./media/container-instances-jenkins/jenkins-job-01.png)

3. W obszarze **kompilacji**, wybierz opcję **Dodaj krok kompilacji** i wybierz **wykonaj powłokę**. Wprowadź `echo "aci-demo"` jako polecenie.

   !["Kompilacja" kartę z opcjami dla kroku kompilacji](./media/container-instances-jenkins/jenkins-job-02.png)

5. Wybierz pozycję **Zapisz**.

## <a name="run-the-build-job"></a>Uruchamianie zadania kompilacji

Aby przetestować zadanie kompilacji i sprawdź usługi Azure Container Instances jako platformę kompilacji, należy ręcznie uruchomić kompilację.

1. Wybierz **Kompiluj teraz** można uruchomić zadania kompilacji. Trwa kilka minut, zanim zadanie do uruchomienia. Powinny zostać wyświetlone stanu, która jest podobna do poniższej ilustracji:

   !["Historia kompilacji" informacje o stanie zadania](./media/container-instances-jenkins/jenkins-job-status.png)

2. Gdy zadanie jest uruchomione, otwórz witrynę Azure portal i przyjrzyj się grupa zasobów usługi Jenkins. Powinieneś zobaczyć, że utworzono wystąpienie kontenera. Zadania narzędzia Jenkins działa wewnątrz tego wystąpienia.

   ![Wystąpienia kontenera w grupie zasobów](./media/container-instances-jenkins/jenkins-aci.png)

3. Po uruchomieniu narzędzia Jenkins większą liczbę zadań niż skonfigurowana liczba executors narzędzia Jenkins (wartość domyślna 2) są tworzone wiele wystąpień kontenera.

   ![Nowo utworzony kontener wystąpień](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po ukończeniu wszystkich zadań kompilacji, wystąpienia kontenera są usuwane.

   ![Grupa zasobów, usługa container instances usunięte](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Jenkins na platformie Azure, zobacz [platformy Azure i usługi Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md