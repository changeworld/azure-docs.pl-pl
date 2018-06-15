---
title: Publikowanie za pomocą narzędzia Azure usługa w chmurze | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu publikowania projektów usług w chmurze platformy Azure przy użyciu programu Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1a07b6e4-3678-4cbf-b37e-4520b402a3d9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: bde00dfbf4a7ffde90d1a9a3d57d3a2decf74cad
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799468"
---
# <a name="publishing-a-cloud-service-using-visual-studio"></a>Publikowanie usługi w chmurze przy użyciu programu Visual Studio

Visual Studio można opublikować aplikację bezpośrednio na platformie Azure, obsługę zarówno tymczasowych i produkcyjnych środowisk usługi w chmurze. Podczas publikowania, wybierz środowisko wdrażania i konto magazynu jest używane tymczasowo pakietu wdrożeniowego.

Opublikuj zmiany przyrostowo dla poszczególnych ról sieci web w gdy w przypadku tworzenia i testowania aplikacji Azure, można użyć narzędzia Web Deploy. Po opublikowaniu aplikacji w środowisku wdrażania narzędzia Web Deploy umożliwia wdrażanie zmian bezpośrednio do maszyny wirtualnej, na którym jest uruchomiona rola sieci web. Nie masz pakietu i opublikować całą aplikację Azure zawsze chcesz zaktualizować roli sieci web do testowania zmiany. Z tej metody może mieć zmiany roli sieci web dostępnych w chmurze na potrzeby testowania bez oczekiwania na zostały opublikowane w środowisku wdrażania aplikacji.

Użyj poniższych procedur, aby opublikować aplikację usługi Azure i zaktualizuj rolę sieci web za pomocą narzędzia Web Deploy:

- Publikowanie lub pakietu aplikacji Azure w programie Visual Studio
- Zaktualizuj rolę sieci web w ramach projektowania i testowania cyklu

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publikowanie lub pakietu aplikacji Azure w programie Visual Studio

Podczas publikowania aplikacji platformy Azure, możesz wybrać jedną z następujących czynności:

- Tworzenie pakietu usług: ten pakiet i pliku konfiguracji usługi można użyć do publikowania aplikacji w środowisku wdrażania z [portalu Azure](https://portal.azure.com).

- Opublikować projekt platformy Azure w programie Visual Studio: Aby opublikować aplikację bezpośrednio na platformie Azure, należy użyć Kreatora publikowania. Aby uzyskać informacje, zobacz [Kreator publikowania aplikacji Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Aby utworzyć pakiet usługi w programie Visual Studio

1. Gdy wszystko będzie gotowe opublikować aplikację, otwórz Eksploratora rozwiązań, otwórz menu skrótów projektu platformy Azure, który zawiera role i wybierz polecenie publikowania.

1. Aby utworzyć pakietu usług, wykonaj następujące kroki:

   a. Menu skrótów projektu platformy Azure, wybierz **pakietu**.

   b. W **pakietu aplikacji Azure** okno dialogowe Wybieranie konfiguracji usługi, dla której chcesz utworzyć pakiet, a następnie wybierz konfigurację kompilacji.

   c. (Opcjonalnie) Aby włączyć funkcję pulpitu zdalnego dla usługi w chmurze po opublikowaniu, zaznacz **Włącz pulpit zdalny dla wszystkich ról**, a następnie wybierz **ustawienia** można skonfigurować poświadczeń pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [włączyć Podłączanie pulpitu zdalnego dla roli w usług Azure Cloud Services przy użyciu programu Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

      Jeśli chcesz debugować usługi w chmurze po opublikowaniu, Włącz debugowanie zdalne wybierając **Włącz zdalny debuger dla wszystkich ról**.

   d. Aby utworzyć pakiet, należy wybrać **pakietu** łącza.

      Eksplorator plików zawiera lokalizację pliku nowo utworzony pakiet. Możesz skopiować tej lokalizacji, tak aby można go używać w portalu Azure.

   e. Aby opublikować środowiska wdrażania tego pakietu, należy użyć tej lokalizacji jako lokalizacja pakietu podczas tworzenia usługi w chmurze i wdrażania tego pakietu do środowiska przy użyciu portalu Azure.

1. (Opcjonalnie) Aby anulować proces wdrażania dla pozycji w dzienniku aktywności, w menu skrótów wybierz **Anuluj i Usuń**. To polecenie powoduje zatrzymanie procesu wdrażania i usuwa środowiska wdrażania z platformy Azure. Aby usunąć środowiska po wdrożeniu, użyj portalu Azure.

1. (Opcjonalnie) Po uruchomiona wystąpienia roli, Visual Studio automatycznie wyświetlany środowiska wdrażania w **usługi w chmurze** węzła w Eksploratorze serwera. W tym miejscu można wyświetlić stan wystąpień poszczególnych ról. Zobacz [Azure zarządzanie zasobami za pomocą Eksploratora chmury](vs-azure-tools-resources-managing-with-cloud-explorer.md). Na poniższej ilustracji przedstawiono wystąpienia roli, gdy są one nadal w stanie inicjowanie:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Zaktualizuj rolę sieci web w ramach projektowania i testowania cyklu

Jeśli infrastruktura wewnętrzna aplikacji jest stabilna, ale role sieci web trzeba więcej często uaktualniać, służy narzędzie Web Deploy można zaktualizować roli sieci web w projekcie. Narzędzie Web Deploy jest przydatne, gdy nie chcesz ponowne skompilowanie i wdrożenie role procesów roboczych zaplecza lub jeśli masz wiele ról sieci web i zaktualizować tylko jedną z ról sieci web.

### <a name="requirements-for-using-web-deploy"></a>Wymagania dotyczące korzystania z narzędzia Web Deploy

- **Programowanie i testowanie tylko celów**: zmiany są dokonywane bezpośrednio do maszyny wirtualnej, którym jest uruchomiona rola sieci web. Jeśli tej maszyny wirtualnej musi zostać przetworzony ponownie, zmiany zostaną utracone, ponieważ oryginalny pakiet opublikowaną jest używana do odtworzenia dla roli maszyny wirtualnej. Ponownie opublikować aplikację, aby pobrać najnowsze zmiany roli sieci web.

- **Można aktualizować tylko role sieci web**: nie można zaktualizować roli proces roboczy. Ponadto nie można zaktualizować `RoleEntryPoint` w `web role.cs`.

- **Może obsługiwać tylko jedno wystąpienie roli sieci web**: nie może istnieć wiele wystąpień dowolnej roli sieci web w środowisku wdrażania. Jednak wielu role sieci web każdego z tylko jednym wystąpieniem są obsługiwane.

- **Włączenie połączeń pulpitu zdalnego**: Umożliwia wymaganie to narzędzie Web Deploy na potrzeby połączenia z maszyną wirtualną, aby wdrożyć zmiany na serwerze z systemem Internet Information Services (IIS) i hasło użytkownika. Ponadto konieczne może się połączyć z maszyną wirtualną, aby dodać zaufanego certyfikatu usług IIS na tej maszynie wirtualnej. (Ten certyfikat zapewnia, że połączenia zdalnego dla usług IIS, który jest używany przez narzędzie Web Deploy jest bezpieczne.)

W poniższej procedurze przyjęto, że używasz **publikowanie aplikacji platformy Azure** kreatora.

### <a name="enable-web-deploy-when-you-publish-your-application"></a>Włącz narzędzia Web Deploy podczas publikowania aplikacji

1. Aby włączyć **Włącz narzędzia Web Deploy dla wszystkich ról sieci web** opcji, należy najpierw skonfigurować połączenia pulpitu zdalnego. Wybierz **Włącz pulpit zdalny** dla wszystkich ról, a następnie wprowadź poświadczenia, które służy do łączenia zdalnie w **konfigurację usług pulpitu zdalnego** pojawi się okno. Zobacz [włączyć Podłączanie pulpitu zdalnego dla roli w usług Azure Cloud Services przy użyciu programu Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

1. Aby włączyć narzędzia Web Deploy w dla wszystkich ról sieci web w aplikacji, wybierz **Włącz narzędzia Web Deploy dla wszystkich ról sieci web**.

    Żółty trójkąt ostrzeżenie zostanie wyświetlone. Narzędzie Web Deploy używa certyfikatów niezaufanych, podpisem domyślnie, co nie jest zalecane do przekazywania poufnych danych. Jeśli trzeba zabezpieczyć ten proces dla danych poufnych, można dodać certyfikat SSL służący do połączenia narzędzia Web Deploy. Ten certyfikat musi być zaufanym certyfikatem. Aby uzyskać więcej informacji, zobacz [zabezpieczyć sieci web wdrażanie](#make-web-deploy-secure).

1. Wybierz **dalej** pokazanie **Podsumowanie** ekranu, a następnie wybierz pozycję **publikowania** do wdrożenia usługi w chmurze.

    Usługi w chmurze jest opublikowana. Maszyny wirtualnej, która jest tworzona ma włączone dla usług IIS tak, aby narzędzia Web Deploy może posłużyć do zaktualizowania role sieci web bez ponownego publikowania ich połączeń zdalnych.

   > [!NOTE]
   > Jeśli masz więcej niż jedno wystąpienie skonfigurowany dla roli sieci web, zostanie wyświetlony komunikat ostrzegawczy informujący, że każda rola sieci web jest ograniczone do jednego wystąpienia tylko w pakiecie, który służy do publikowania aplikacji. Kliknij przycisk **OK**, aby kontynuować. Jak już wspomniano w sekcji wymagań, może mieć więcej niż jednej roli sieci web, ale tylko jedno wystąpienie każdej roli.

### <a name="update-your-web-role-by-using-web-deploy"></a>Aktualizacja roli sieci web za pomocą narzędzia Web Deploy

1. Aby korzystać z narzędzia Web Deploy, wprowadzać zmiany kodu do projektu dla żadnej z ról użytkownika sieci web w programie Visual Studio, który chcesz opublikować, a następnie kliknij prawym przyciskiem myszy węzeł tego projektu w rozwiązaniu i wskaż polecenie **publikowania**. **Publikowanie w sieci Web** zostanie wyświetlone okno dialogowe.

1. (Opcjonalnie) Jeśli dodano zaufany certyfikat SSL do użycia dla połączeń zdalnych dla usług IIS, można wyczyścić **niezaufany Zezwalaj** pole wyboru. Aby dowiedzieć się, jak dodać certyfikat, aby zabezpieczyć narzędzia Web Deploy, zobacz sekcję **do upewnij sieci Web wdrażanie bezpiecznych** dalszej części tego artykułu.

1. Aby korzystać z narzędzia Web Deploy, mechanizmu publikowania wymaga nazwy użytkownika i hasło, które można skonfigurować dla połączenia pulpitu zdalnego po pierwszym opublikowaniu pakietu.

   a. W **nazwy użytkownika**, wprowadź nazwę użytkownika.

   b. W **hasło**, wprowadź hasło.

   c. (Opcjonalnie) Jeśli chcesz zapisać to hasło do tego profilu, wybierz **Zapisz hasło**.

1. Aby opublikować zmiany roli sieci web, wybierz **publikowania**.

    Wyświetla wiersz stanu **publikowania uruchomiona**. Po zakończeniu publikowania **publikowanie zakończyło się pomyślnie** pojawi się. Zmiany zostały wdrożone obecnie do roli sieci web na maszynie wirtualnej. Teraz można uruchomić aplikacji platformy Azure w środowisku platformy Azure, aby sprawdzić zmiany.

### <a name="make-web-deploy-secure"></a>Zabezpieczyć wdrażania w sieci web

1. Narzędzie Web Deploy używa certyfikatów niezaufanych, podpisem domyślnie, co nie jest zalecane do przekazywania poufnych danych. Jeśli trzeba zabezpieczyć ten proces dla danych poufnych, można dodać certyfikat SSL służący do połączenia narzędzia Web Deploy. Ten certyfikat musi być zaufany certyfikat, który można uzyskać od urzędu certyfikacji (CA).

    Aby zabezpieczyć narzędzia Web Deploy dla każdej maszyny wirtualnej dla każdej z ról użytkownika sieci web, musisz przekazać zaufanego certyfikatu, który ma być używany dla sieci web wdrożenia do portalu Azure. Ten certyfikat zapewnia, że certyfikat został dodany do maszyny wirtualnej, która jest tworzona dla roli sieci web podczas publikowania aplikacji.

1. Aby dodać zaufany certyfikat SSL do usługi IIS mają używać dla połączeń zdalnych, wykonaj następujące kroki:

   a. Aby połączyć maszynę wirtualną, która jest uruchomiona rola sieci web, wybierz wystąpienie roli sieci web w **Eksplorator chmury** lub **Eksploratora serwera**, a następnie wybierz pozycję **łączyć się przy użyciu pulpitu zdalnego**  polecenia. Aby uzyskać szczegółowe instrukcje na temat nawiązywania połączenia z maszyną wirtualną, zobacz [włączyć Podłączanie pulpitu zdalnego dla roli w usług Azure Cloud Services przy użyciu programu Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md). Przeglądarka monit o pobranie `.rdp` pliku.

   b. Aby dodać certyfikat SSL, otwórz usługę zarządzania w Menedżerze usług IIS. W Menedżerze usług IIS, Włącz protokół SSL, otwierając **powiązania** łącze w **akcji** okienka. **Dodawanie powiązania witryny** zostanie wyświetlone okno dialogowe. Wybierz **Dodaj**, a następnie wybierz pozycję HTTPS w **typu** listy rozwijanej. W **certyfikat SSL** wybierz certyfikat SSL, aby były podpisane przez urząd certyfikacji i został przekazany do portalu Azure. Aby uzyskać więcej informacji, zobacz [skonfigurować ustawienia połączenia dla usługi zarządzania](http://go.microsoft.com/fwlink/?LinkId=215824).

      > [!NOTE]
      > Jeśli dodajesz zaufanego certyfikatu SSL, żółty trójkąt ostrzeżenia nie jest już widoczna w **Kreator publikowania**.

## <a name="include-files-in-the-service-package"></a>Dołącz pliki w pakiecie usługi

Konieczne może być obejmują określonych plików w pakiecie usługi, aby były one dostępne na maszynie wirtualnej, który jest tworzony dla roli. Na przykład można dodać `.exe` lub `.msi` pliku, który jest używany przez skrypt uruchamiania do pakietu usług. Lub może być konieczne dodanie zestawu, który wymaga projektu roli sieć web, jak rola lub procesu roboczego. Aby uwzględnić pliki, muszą zostać dodane do rozwiązania dla aplikacji platformy Azure.

1. Aby dodać zestawu do pakietu usługi, użyj następujących kroków:

   a. W **Eksploratora rozwiązań**, otwórz węzeł projektu dla projektu, w którym brakuje przywoływanego zestawu.
   b. Aby dodać zestaw do projektu, otwórz menu skrótów **odwołania** folder, a następnie wybierz **Dodaj odwołanie**. Zostanie wyświetlone okno dialogowe Dodaj odwołanie.
   c. Wybierz odwołanie, które chcesz dodać, a następnie wybierz pozycję **OK**. Odwołanie zostanie dodany do listy w obszarze **odwołania** folderu.
   d. Otwórz menu skrótów dla zestawu, który został dodany i wybierz polecenie **właściwości**. **Właściwości** zostanie wyświetlone okno.

      Do uwzględnienia w tym zestawie w pakiecie usługi **kopii lokalnej listy** wybierz **True**.
1. W **Eksploratora rozwiązań** Otwórz węzeł projektu dla projektu, w którym brakuje przywoływanego zestawu.

1. Aby dodać zestaw do projektu, otwórz menu skrótów **odwołania** folder, a następnie wybierz **Dodaj odwołanie**. **Dodaj odwołanie** zostanie wyświetlone okno dialogowe.

1. Wybierz odwołanie, które chcesz dodać, a następnie wybierz pozycję **OK** przycisku.

    Odwołanie zostanie dodany do listy w obszarze **odwołania** folderu.

1. Otwórz menu skrótów dla zestawu, który został dodany i wybierz polecenie **właściwości**. Zostanie wyświetlone okno właściwości.

1. Do uwzględnienia w tym zestawie w pakiecie usługi **Kopiuj lokalnie** wybierz **True**.

1. Aby uwzględnić pliki w pakiecie usługi, które zostały dodane do projektu roli sieci web, otwórz menu skrótów dla pliku, a następnie wybierz **właściwości**. Z **właściwości** okna, wybierz **zawartości** z **Akcja kompilacji** pola listy.

1. Aby uwzględnić pliki w pakiecie usługi, które zostały dodane do projektu roli proces roboczy, otwórz menu skrótów dla pliku, a następnie wybierz **właściwości**. Z **właściwości** okna, wybierz **Kopiuj, jeśli nowszy** z **Kopiuj do katalogu wyjściowego** pola listy.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat publikowania na platformie Azure w programie Visual Studio, zobacz [Kreator publikowania aplikacji Azure](vs-azure-tools-publish-azure-application-wizard.md).
