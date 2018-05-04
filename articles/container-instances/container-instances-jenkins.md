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
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Użyj wystąpień kontenera Azure jako Wpięć agenta kompilacji

Wystąpień kontenera platformy Azure zapewniają na żądanie, burstable i izolowane środowisko uruchamiania konteneryzowanych obciążeń. Z powodu tych atrybutów wystąpień kontenera Azure należy doskonałą platformą dla zadania kompilacji Wpięć na dużą skalę. Ten dokument przeprowadzi Cię przez wdrażanie i przy użyciu serwera Wpięć, który jest wstępnie skonfigurowana z ACI jako miejsce docelowe kompilacji.

Aby uzyskać więcej informacji na wystąpień kontenera platformy Azure, zobacz [o wystąpień kontenera Azure][about-aci].

## <a name="deploy-jenkins-server"></a>Wdrażanie serwera Wpięć

W portalu Azure wybierz **Utwórz zasób** i wyszukaj **Wpięć**. Wybierz oferty Wpięć z wydawcą z **Microsoft** i wybierz **Utwórz**.

Wprowadź następujące informacje w formularzu podstawowe informacje o, a następnie kliknij przycisk **OK** po zakończeniu.

- **Nazwa** — Nazwa wdrożenia Wpięć.
- **Nazwa użytkownika** — ta nazwa użytkownika jest używana jako administrator Wpięć maszyny wirtualnej.
- **Typ uwierzytelniania** — SSH klucz publiczny jest zalecane. Jeśli zaznaczone, skopiuj klucz publiczny SSH, na które będą używane podczas logowania się do maszyny wirtualnej Wpięć.
- **Subskrypcja** — Wybierz subskrypcję platformy Azure.
- **Grupa zasobów** — Utwórz nowy lub wybierz istniejącą grupę zasobów.
- **Lokalizacja** — wybierz lokalizację na serwerze Wpięć.

![Podstawowe ustawienia wdrażania portalu Wpięć](./media/container-instances-jenkins/jenkins-portal-01.png)

W formularzu dodatkowe ustawienia należy wykonać następujące elementy:

- **Rozmiar** — wybierz opcję odpowiednią rozmiaru Wpięć maszyny wirtualnej.
- **Typ dysku maszyny Wirtualnej** — Określ dysk twardy (dysk twardy) lub dysków SSD (SSD) dla serwera Wpięć.
- **Sieć wirtualna** -(opcjonalnie) zaznacz wirtualnej sieci można zmodyfikować ustawienia domyślne.
- **Podsieci** — wybierz podsieci, sprawdź informacje, a następnie wybierz **OK**.
- **Publiczny adres IP** -wybranie publiczny adres IP umożliwia nadaj mu nazwę niestandardową, skonfiguruj jednostki SKU i metoda przydziału.
- **Etykieta nazwy domeny** — Określ wartość do utworzenia w pełni kwalifikowany adres URL do Wpięć maszyny wirtualnej.
- **Typ zlecenia Wpięć** — wybierz typ żądanej wersji z opcji: LTS, co tydzień kompilacji lub zweryfikować Azure.

![Dodatkowe ustawienia wdrażania portalu Wpięć](./media/container-instances-jenkins/jenkins-portal-02.png)

Usługi integracji podmiotu zabezpieczeń, wybierz **Auto(MSI)** mają [tożsamości zarządzanych usługi Azure] [ managed-service-identity] automatyczne tworzenie tożsamość uwierzytelniania dla wystąpienia Wpięć. Umożliwia ręczne dostawcy własne poświadczenia główne usługi.

Agenci chmurze skonfiguruj oparte na chmurze platforma dla zadania kompilacji Wpięć. Dla tego dokumentu wybierz ACI. W agencie chmury ACI każde zadanie kompilacji Wpięć jest uruchamiane w wystąpieniu kontenera platformy Azure.

![Ustawienia integracji chmury wdrożenia portalu Wpięć](./media/container-instances-jenkins/jenkins-portal-03.png)

Gdy odbywa się za pomocą ustawienia integracji, kliknij przycisk **OK**, a następnie **OK** ponownie na podsumowania weryfikacji. Kliknij przycisk **Utwórz** na warunkach Podsumowanie użycia. Serwer Wpięć zajmuje kilka minut, aby wdrożyć.

## <a name="configure-jenkins"></a>Konfigurowanie usługi Jenkins

W portalu Azure przejdź do grupy zasobów Wpięć, wybierz maszynę wirtualną, Wpięć i zanotuj nazwę DNS.

![Instrukcje logowania Wpięć](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Przeglądarka nazwa DNS Wpięć maszyny Wirtualnej i skopiuj zwracany ciąg SSH.

![Instrukcje logowania Wpięć](./media/container-instances-jenkins/jenkins-portal-04.png)

Otwórz terminal sesji w systemie deweloperskim i Wklej w ciągu SSH w ostatnim kroku. Podana nazwa użytkownika w przypadku wdrażania serwera Wpięć aktualizacji 'username'.

Po nawiązaniu połączenia, uruchom następujące polecenie, aby pobrać hasło administratora początkowej.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Pozostaw sesji SSH i tunelu uruchomiona, a następnie przejdź do http://localhost:8080 w przeglądarce. Wklej hasło administratora początkowa w polu, jak pokazano na poniższej ilustracji. Wybierz **Kontynuuj** po zakończeniu.

![Odblokuj Wpięć](./media/container-instances-jenkins/jenkins-portal-05.png)

Wybierz **Instalowanie wtyczki sugerowane** można zainstalować wszystkie zalecane Wpięć wtyczek.

![Instalowanie wtyczki Wpięć](./media/container-instances-jenkins/jenkins-portal-06.png)

Utwórz nowe konto użytkownika Administrator. To konto jest używane do logowania się do i pracy z wystąpieniem Wpięć.

![Utwórz użytkownika Wpięć](./media/container-instances-jenkins/jenkins-portal-07.png)

Wybierz **zapisać i zakończenia** po zakończeniu, a następnie **Rozpoczynanie korzystania z Wpięć** w celu ukończenia konfiguracji.

Wpięć jest teraz skonfigurowane i gotowe do tworzenia i wdrażania kodu. Na przykład prostą aplikację Java służy do pokazują kompilacji Wpięć na wystąpień kontenera platformy Azure.

## <a name="create-build-job"></a>Tworzenie zadania kompilacji

Przy użyciu obrazu kontenera jako Wpięć kompilowania docelowej, należy określić obraz, który zawiera wszystkie narzędzia niezbędne do pomyślnego utworzenia kompilacji. Obraz, zaznacz **Zarządzanie Wpięć** > **skonfigurować System** i przewiń w dół do **chmury** sekcji. Na przykład zaktualizować wartość obrazu Docker `microsoft/java-on-azure-jenkins-slave`.

Po zakończeniu kliknij przycisk **zapisać** aby wrócić do pulpitu nawigacyjnego Wpięć.

![Konfiguracja chmury Wpięć](./media/container-instances-jenkins/jenkins-aci-image.png)

Teraz utworzyć Wpięć zadania kompilacji. Wybierz **nowy element**, nadaj nazwę projektu kompilacji takich jak `aci-java-demo`, wybierz pozycję **stylu projektu**i kliknij przycisk **OK**.

![Tworzenie zadania usługi Jenkins](./media/container-instances-jenkins/jenkins-new-job.png)

W obszarze **ogólne**, upewnij się, że **Ogranicz, w którym można uruchomić tego projektu** jest zaznaczone. Wprowadź `linux` dla wyrażenia etykiety. Taka konfiguracja powoduje, że to zadanie kompilacji zostanie uruchomiona w chmurze ACI.

![Tworzenie zadania usługi Jenkins](./media/container-instances-jenkins/jenkins-job-01.png)

W obszarze zarządzania kodem źródłowym, wybierz `Git` , a następnie wprowadź `https://github.com/spring-projects/spring-petclinic.git` dla adresu URL repozytorium. To repozytorium GitHub zawiera przykładowy kod aplikacji.

![Dodaj do zadania Wpięć kodu źródłowego](./media/container-instances-jenkins/jenkins-job-02.png)

W obszarze kompilacji, wybierz **dodać kroku kompilacji** i wybierz `Invoke top-level Maven targets`. Wprowadź `package` jako cel kroku kompilacji.

![Dodaj Wpięć kroku kompilacji](./media/container-instances-jenkins/jenkins-job-03.png)

Wybierz **zapisać** po zakończeniu.

## <a name="run-the-build-job"></a>Uruchom zadanie kompilacji

Aby przetestować zadania kompilacji i obserwować wystąpień kontenera Azure jako platforma kompilacji, należy ręcznie uruchomić kompilację.

Wybierz **kompilacji teraz** można rozpocząć zadania kompilacji. Trwa kilka minut, aż zadanie można uruchomić podczas uruchamiania, powinien zostać wyświetlony stan podobne do następujących obrazów.

![Dodaj Wpięć kroku kompilacji](./media/container-instances-jenkins/jenkins-job-status.png)

Gdy zadanie jest uruchomione, otwarcie portalu Azure i przyjrzyj się Wpięć grupy zasobów. Powinna zostać wyświetlona utworzono wystąpienie kontenera platformy Azure. Znajduje się wewnątrz tego wystąpienia, które jest wykonywane zadanie Wpięć.

![Kompilacje Wpięć w ACI](./media/container-instances-jenkins/jenkins-aci.png)

Zgodnie z Wpięć uruchamia zadania więcej niż skonfigurowana liczba modułów Wpięć (domyślnie: 2), są tworzone wielu wystąpień kontenera platformy Azure.

![Kompilacje Wpięć w ACI](./media/container-instances-jenkins/jenkins-aci-multi.png)

Po ukończeniu wszystkich zadań kompilacji wystąpień kontenera platformy Azure są usuwane.

![Kompilacje Wpięć w ACI](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o Wpięć na platformy Azure, zobacz [Azure i Wpięć][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md