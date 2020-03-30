---
title: Jenkins kompilacji na wystąpienie kontenera
description: Dowiedz się, jak skonfigurować serwer usługi Jenkins do uruchamiania zadań kompilacji na żądanie w wystąpieniach kontenera platformy Azure
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617972"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Używanie wystąpień kontenerów platformy Azure jako agenta kompilacji usługi Jenkins

Wystąpienia kontenera platformy Azure (ACI) zapewnia na żądanie, burstable i izolowane środowisko do uruchamiania konteneryzowanych obciążeń. Ze względu na te atrybuty ACI tworzy doskonałą platformę do uruchamiania zadań kompilacji usługi Jenkins na dużą skalę. W tym artykule oprzeszono wdrażanie i używanie serwera jenkins, który jest wstępnie skonfigurowany z ACI jako miejsce docelowe kompilacji.

Aby uzyskać więcej informacji na temat wystąpień kontenerów platformy Azure, zobacz [Informacje o wystąpieniach kontenera platformy Azure.](../container-instances/container-instances-overview.md)

## <a name="deploy-a-jenkins-server"></a>Wdrażanie serwera usługi Jenkins

1. W witrynie Azure portal wybierz pozycję **Utwórz zasób** i wyszukaj **pozycję Jenkins**. Wybierz ofertę jenkinsa u wydawcy **firmy Microsoft,** a następnie wybierz pozycję **Utwórz**.

2. Wprowadź następujące informacje w formularzu **Podstawy,** a następnie wybierz przycisk **OK**.

   - **Nazwa**: Wprowadź nazwę wdrożenia usługi Jenkins.
   - **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora maszyny wirtualnej usługi Jenkins.
   - **Typ uwierzytelniania**: Zalecamy klucz publiczny SSH do uwierzytelniania. Jeśli wybierzesz tę opcję, wklej klucz publiczny SSH, który ma być używany do logowania się do maszyny wirtualnej usługi Jenkins.
   - **Subskrypcja**: Wybierz subskrypcję platformy Azure.
   - **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.
   - **Lokalizacja**: Wybierz lokalizację dla serwera Jenkins.

   ![Podstawowe ustawienia wdrożenia portalu usługi Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. W formularzu **Ustawienia dodatkowe** wypełnij następujące elementy:

   - **Rozmiar:** Wybierz odpowiednią opcję zmiany rozmiaru dla maszyny wirtualnej usługi Jenkins.
   - **Typ dysku maszyny Wirtualnej**: Określ **dysk twardy** (dysk twardy) lub **dysk SSD** (dysk PÓŁPRZEWODNIKOWY) dla serwera Jenkins.
   - **Sieć wirtualna**: Wybierz strzałkę, jeśli chcesz zmodyfikować ustawienia domyślne.
   - **Podsieci**: Wybierz strzałkę, sprawdź informacje i wybierz **przycisk OK**.
   - **Publiczny adres IP**: Wybierz strzałkę, aby nadać publicznemu adresowi IP nazwę niestandardową, skonfigurować jednostkę SKU i ustawić metodę przypisania.
   - **Etykieta nazwy domeny:** Określ wartość, aby utworzyć w pełni kwalifikowany adres URL do maszyny wirtualnej usługi Jenkins.
   - **Typ wydania usługi Jenkins:** Wybierz żądany typ wersji z opcji: **LTS**, **Kompilacja tygodniowa**lub **Zweryfikowana platforma Azure**.

   ![Dodatkowe ustawienia wdrożenia portalu usługi Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. W przypadku integracji z jednostką usługi wybierz **opcję Auto(MSI),** aby [zarządzane tożsamości zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) automatycznie utworzyć tożsamość uwierzytelniania dla wystąpienia usługi Jenkins. Wybierz **podręcznik,** aby podać własne poświadczenia jednostki usługi.

5. Agenci w chmurze konfigurują platformę chmurową dla zadań kompilacji usługi Jenkins. Ze względu na ten artykuł wybierz **ACI**. Za pomocą agenta chmury ACI każde zadanie kompilacji usługi Jenkins jest uruchamiane w wystąpieniu kontenera.

   ![Ustawienia integracji w chmurze dla wdrażania portalu usługi Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Po zakończeniu pracy z ustawieniami integracji wybierz przycisk **OK**, a następnie ponownie wybierz przycisk **OK** w podsumowaniu sprawdzania poprawności. Wybierz **pozycję Utwórz** w **podsumowaniu warunków użytkowania.** Wdrożenie serwera usługi Jenkins zajmuje kilka minut.

## <a name="configure-jenkins"></a>Konfigurowanie usługi Jenkins

1. W witrynie Azure portal przejdź do grupy zasobów usługi Jenkins, wybierz maszynę wirtualną usługi Jenkins i zanotuj nazwę DNS.

   ![Nazwa DNS w szczegółach dotyczących maszyny wirtualnej usługi Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Przejdź do nazwy DNS maszyny Wirtualnej usługi Jenkins i skopiuj zwrócony ciąg SSH.

   ![Instrukcje logowania jenkins z ciągiem SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Otwórz sesję terminalu w systemie dewelopera i wklej ciąg SSH od ostatniego kroku. Zaktualizuj `username` do nazwy użytkownika określonej podczas wdrażania serwera jenkins.

4. Po nawiązaniu połączenia sesji uruchom następujące polecenie, aby pobrać początkowe hasło administratora:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Pozostaw sesję SSH i tunel `http://localhost:8080` uruchomiony i przejdź do przeglądarki. Wklej początkowe hasło administratora do pola, a następnie wybierz pozycję **Kontynuuj**.

   ![Ekran "Unlock Jenkins" z polem hasła administratora](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Wybierz **zainstaluj sugerowane wtyczki,** aby zainstalować wszystkie zalecane wtyczki Jenkins.

   ![Ekran "Dostosuj Jenkins" z wybranym "Zainstaluj sugerowane wtyczki"](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Utwórz konto użytkownika administratora. To konto służy do logowania się do wystąpienia usługi Jenkins i pracy z nią.

   ![Ekran "Utwórz pierwszego użytkownika administracyjnego" z wypełnionymi poświadczeniami](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Wybierz **pozycję Zapisz i zakończ**, a następnie wybierz pozycję **Rozpocznij korzystanie z usługi Jenkins,** aby ukończyć konfigurację.

Jenkins jest teraz skonfigurowany i gotowy do tworzenia i wdrażania kodu. W tym przykładzie prosta aplikacja Java jest używana do wykazania kompilacji usługi Jenkins na wystąpieniach kontenera platformy Azure.

## <a name="create-a-build-job"></a>Tworzenie zadania kompilacji

Teraz zadanie kompilacji usługi Jenkins jest tworzony, aby zademonstrować jenkins buduje na wystąpienie kontenera platformy Azure.

1. Wybierz **pozycję Nowy element**, nadaj projektowi kompilacji nazwę taką jak **aci-demo**, wybierz **opcję Freestyle project**i wybierz **PRZYCISK OK**.

   ![Pole dla nazwy zadania kompilacji i listy typów projektów](./media/container-instances-jenkins/jenkins-new-job.png)

2. W obszarze **Ogólne**upewnij się, że **wybrano opcję Ogranicz miejsce, w którym można uruchomić ten projekt.** Wprowadź **linux** dla wyrażenia etykiety. Ta konfiguracja gwarantuje, że to zadanie kompilacji jest uruchamiane w chmurze ACI.

   ![Karta "Ogólne" ze szczegółami konfiguracji](./media/container-instances-jenkins/jenkins-job-01.png)

3. W obszarze **Kompilacja**wybierz pozycję **Dodaj krok kompilacji** i wybierz pozycję **Wykonaj powłokę**. Wprowadź `echo "aci-demo"` jako polecenie.

   ![Zakładka "Buduj" z wyborami dla kroku kompilacji](./media/container-instances-jenkins/jenkins-job-02.png)

5. Wybierz **pozycję Zapisz**.

## <a name="run-the-build-job"></a>Uruchamianie zadania kompilacji

Aby przetestować zadanie kompilacji i obserwować wystąpienia kontenera platformy Azure jako platformę kompilacji, ręcznie uruchom kompilację.

1. Wybierz **pozycję Zbuduj teraz,** aby rozpocząć zadanie kompilacji. Rozpoczęcie zadania zajmuje kilka minut. Powinien zostać wyświetlony stan podobny do następującego obrazu:

   ![Informacje o historii kompilacji ze stanem zadania](./media/container-instances-jenkins/jenkins-job-status.png)

2. Gdy zadanie jest uruchomione, otwórz witrynę Azure portal i spójrz na grupę zasobów usługi Jenkins. Należy zobaczyć, że wystąpienie kontenera zostało utworzone. Zadanie Jenkins jest uruchomiony wewnątrz tego wystąpienia.

   ![Wystąpienie kontenera w grupie zasobów](./media/container-instances-jenkins/jenkins-aci.png)

3. Ponieważ usługa Jenkins uruchamia więcej zadań niż skonfigurowana liczba executorów usługi Jenkins (domyślnie 2), tworzona jest wiele wystąpień kontenerów.

   ![Nowo utworzone wystąpienia kontenerów](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po zakończeniu wszystkich zadań kompilacji wystąpienia kontenera są usuwane.

   ![Grupa zasobów z usuniętymi wystąpieniami kontenerów](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli wystąpią jakiekolwiek błędy z wtyczkami jenkins, zgładź problem w [JIRA usługi Jenkins](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągła integracja/ciągłe wdrażanie w usłudze Azure App Service](java-deploy-webapp-tutorial.md)