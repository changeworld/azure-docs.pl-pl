---
title: Jenkins kompilacja na wystąpieniu kontenera
description: Dowiedz się, jak skonfigurować serwer Jenkins do uruchamiania zadań kompilacji na żądanie w Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: e63ade82d4efeed40a9fba6f11d16131e8c728e7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484068"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Użyj Azure Container Instances jako agenta kompilacji Jenkins

Azure Container Instances (ACI) udostępnia środowisko na żądanie, z możliwością obliczeniową i izolowaną do uruchamiania obciążeń kontenerowych. Ze względu na te atrybuty ACI tworzy doskonałą platformę do uruchamiania zadań kompilacji Jenkins w dużej skali. W tym artykule opisano wdrażanie i używanie serwera Jenkins, który jest wstępnie skonfigurowany za pomocą ACI jako element docelowy kompilacji.

Aby uzyskać więcej informacji na temat Azure Container Instances, zobacz [Informacje o Azure Container Instances][about-aci].

## <a name="deploy-a-jenkins-server"></a>Wdrażanie serwera Jenkins

1. W Azure Portal wybierz pozycję **Utwórz zasób** i wyszukaj ciąg **Jenkins**. Wybierz ofertę Jenkins z wydawcą **firmy Microsoft**, a następnie wybierz pozycję **Utwórz**.

2. Wprowadź następujące informacje w formularzu **podstawy** , a następnie wybierz przycisk **OK**.

   - **Nazwa**: Wprowadź nazwę wdrożenia Jenkins.
   - **Nazwa użytkownika**: Wprowadź nazwę użytkownika administratora maszyny wirtualnej Jenkins.
   - **Typ uwierzytelniania**: zalecamy użycie klucza publicznego SSH na potrzeby uwierzytelniania. W przypadku wybrania tej opcji Wklej w kluczu publicznym SSH, który ma być używany do logowania się do maszyny wirtualnej Jenkins.
   - **Subskrypcja**: wybierz subskrypcję platformy Azure.
   - **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.
   - **Lokalizacja**: Wybierz lokalizację serwera Jenkins.

   ![Podstawowe ustawienia wdrożenia portalu Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. W formularzu **Ustawienia dodatkowe** wykonaj następujące czynności:

   - **Rozmiar**: wybierz odpowiednią opcję zmiany rozmiaru maszyny wirtualnej Jenkins.
   - **Typ dysku maszyny wirtualnej**: Określ **dyski** twarde (dysk twardy) lub **SSD** (dysk SSD) dla serwera Jenkins.
   - **Sieć wirtualna**: wybierz strzałkę, jeśli chcesz zmodyfikować ustawienia domyślne.
   - **Podsieci**: wybierz strzałkę, sprawdź informacje i wybierz **przycisk OK**.
   - **Publiczny adres IP**: wybierz strzałkę, aby nadać publicznemu adresowi IP nazwę niestandardową, skonfigurować jednostkę SKU i ustawić metodę przypisywania.
   - **Etykieta nazwy domeny**: Określ wartość, aby utworzyć w pełni kwalifikowany adres URL maszyny wirtualnej Jenkins.
   - **Typ wydania Jenkins**: wybierz żądany typ wydania z opcji: **LTS**, **kompilacja tygodniowa**lub **weryfikacja platformy Azure**.

   ![Dodatkowe ustawienia dla wdrożenia portalu Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. W przypadku integracji głównej usługi wybierz opcję **automatycznie (msi)** , aby [zarządzane tożsamości dla zasobów platformy Azure][managed-identities-azure-resources] automatycznie utworzyły tożsamość uwierzytelniania dla wystąpienia Jenkins. Wybierz pozycję **ręcznie** , aby podać własne poświadczenia nazwy głównej usługi.

5. Agenci chmury konfigurują platformę opartą na chmurze na potrzeby zadań kompilacji Jenkins. Na przykład w tym artykule wybierz pozycję **ACI**. W przypadku ACI Cloud Agent każde zadanie kompilacji Jenkins jest uruchamiane w wystąpieniu kontenera.

   ![Ustawienia integracji chmury dla wdrożenia portalu Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Po zakończeniu pracy z ustawieniami integracji wybierz pozycję **OK**, a następnie ponownie wybierz pozycję **OK** w podsumowaniu walidacji. Wybierz pozycję **Utwórz** na **warunki użytkowania** podsumowanie. Wdrożenie serwera Jenkins może potrwać kilka minut.

## <a name="configure-jenkins"></a>Konfigurowanie usługi Jenkins

1. W Azure Portal przejdź do grupy zasobów Jenkins, wybierz maszynę wirtualną Jenkins i zanotuj nazwę DNS.

   ![Nazwa DNS w szczegółach dotyczących maszyny wirtualnej Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Przejdź do nazwy DNS maszyny wirtualnej Jenkins i skopiuj zwrócony ciąg SSH.

   ![Instrukcje logowania Jenkins z użyciem ciągu SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Otwórz sesję terminalu w systemie deweloperskim i wklej ciąg SSH z ostatniego kroku. Zaktualizuj `username` do nazwy użytkownika określonej podczas wdrażania serwera Jenkins.

4. Po nawiązaniu połączenia Uruchom następujące polecenie, aby pobrać początkowe hasło administratora:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Pozostaw sesję SSH i tunel z systemem, a następnie przejdź do `http://localhost:8080` w przeglądarce. Wklej początkowe hasło administratora do pola, a następnie wybierz pozycję **Kontynuuj**.

   ![Ekran "Odblokuj Jenkins" z polem hasła administratora](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Wybierz pozycję **Zainstaluj sugerowane wtyczki** , aby zainstalować wszystkie zalecane wtyczki Jenkins.

   ![Ekran "Dostosowywanie Jenkins" z wybraną opcją "Zainstaluj sugerowane wtyczki"](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Utwórz konto użytkownika administratora. To konto jest używane do logowania się do wystąpienia usługi Jenkins i pracy z nim.

   ![Ekran "Utwórz pierwszego użytkownika administratora" z poświadczeniami wypełnionymi](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Wybierz pozycję **Zapisz i Zakończ**, a następnie wybierz pozycję **Rozpocznij korzystanie z Jenkins** , aby zakończyć konfigurację.

Jenkins jest teraz skonfigurowana i gotowa do kompilowania i wdrażania kodu. W tym przykładzie prosta aplikacja Java służy do zademonstrowania kompilacji Jenkins na Azure Container Instances.

## <a name="create-a-build-job"></a>Tworzenie zadania kompilacji

Teraz zadanie kompilacji Jenkins jest tworzone, aby przedstawić kompilacje Jenkins w wystąpieniu kontenera platformy Azure.

1. Wybierz pozycję **nowy element**, Nadaj projektowi kompilacji nazwę, taką jak **ACI-Demonstracja**, wybierz pozycję **projekt Freestyle**, a następnie wybierz **przycisk OK**.

   ![Pole dla nazwy zadania kompilacji i lista typów projektów](./media/container-instances-jenkins/jenkins-new-job.png)

2. W obszarze **Ogólne**upewnij się, że wybrano opcję **Ogranicz, gdzie można uruchomić ten projekt** . Wprowadź system **Linux** dla wyrażenia etykiety. Ta konfiguracja gwarantuje, że to zadanie kompilacji zostanie uruchomione w chmurze ACI.

   ![Karta "ogólne" z informacjami o konfiguracji](./media/container-instances-jenkins/jenkins-job-01.png)

3. W obszarze **kompilacja**wybierz pozycję **Dodaj krok kompilacji** , a następnie wybierz pozycję **Wykonaj powłokę**. Wprowadź `echo "aci-demo"` jako polecenie.

   ![Karta "Kompilacja" z wybranymi krokami dla kroku kompilacji](./media/container-instances-jenkins/jenkins-job-02.png)

5. Wybierz pozycję **Zapisz**.

## <a name="run-the-build-job"></a>Uruchamianie zadania kompilacji

Aby przetestować zadanie kompilacji i obserwować Azure Container Instances jako platformę kompilacji, ręcznie Rozpocznij kompilację.

1. Wybierz opcję **Kompiluj teraz** , aby rozpocząć zadanie kompilacji. Uruchomienie zadania może potrwać kilka minut. Powinien zostać wyświetlony stan podobny do następującego:

   ![Informacje o historii kompilacji ze stanem zadania](./media/container-instances-jenkins/jenkins-job-status.png)

2. Gdy zadanie jest uruchomione, Otwórz Azure Portal i sprawdź grupę zasobów Jenkins. Powinna zostać wyświetlona, że utworzono wystąpienie kontenera. Zadanie Jenkins jest uruchomione w tym wystąpieniu.

   ![Wystąpienie kontenera w grupie zasobów](./media/container-instances-jenkins/jenkins-aci.png)

3. Ponieważ Jenkins uruchamia więcej zadań niż skonfigurowana liczba modułów wykonujących Jenkins (domyślnie 2), tworzone są wiele wystąpień kontenerów.

   ![Nowo utworzone wystąpienia kontenera](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po zakończeniu wszystkich zadań kompilacji wystąpienia kontenera są usuwane.

   ![Grupa zasobów z usuniętymi wystąpieniami kontenera](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Jenkins na platformie Azure, zobacz [Azure i Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md
