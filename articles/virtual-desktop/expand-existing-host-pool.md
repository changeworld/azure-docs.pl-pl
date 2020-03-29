---
title: Rozwijanie istniejącej puli hostów o nowe hosty sesji — Azure
description: Jak rozwinąć istniejącą pulę hostów z nowymi hostami sesji na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365223"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Rozwijanie istniejącej puli hostów za pomocą nowych hostów sesji

W miarę zwiększania użycia w puli hostów może być konieczne rozszerzenie istniejącej puli hostów o nowe hosty sesji w celu obsługi nowego obciążenia.

W tym artykule dowiesz się, jak można rozwinąć istniejącą pulę hostów o nowe hosty sesji.

## <a name="what-you-need-to-expand-the-host-pool"></a>Co jest potrzebne do rozszerzenia puli hostów

Przed rozpoczęciem upewnij się, że utworzono maszyny wirtualne hosta hosta i sesji przy użyciu jednej z następujących metod:

- [Oferta portalu Azure Marketplace](./create-host-pools-azure-marketplace.md)
- [Szablon usługi GitHub Usługi Azure Resource Manager](./create-host-pools-arm-template.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](./create-host-pools-powershell.md)

Potrzebne będą również następujące informacje z momentu pierwszego utworzenia maszyn wirtualnych z puli hostów i hostów sesji:

- Prefiks rozmiaru, obrazu i nazwy maszyny Wirtualnej
- Poświadczenia administratora dzierżawy dołączania do domeny i pulpitu wirtualnego systemu Windows
- Nazwa sieci wirtualnej i nazwa podsieci

Następne trzy sekcje są trzy metody, które można użyć do rozszerzenia puli hosta. Możesz to zrobić za pomocą dowolnego narzędzia do wdrażania, z którym czujesz się komfortowo.

>[!NOTE]
>Podczas fazy wdrażania zostaną wyświetlne komunikaty o błędach dla zasobów maszyny wirtualnej hosta poprzedniej sesji, jeśli są one obecnie zamknięte. Te błędy występują, ponieważ platforma Azure nie może uruchomić rozszerzenia DSC programu PowerShell, aby sprawdzić, czy maszyny wirtualne hosta sesji są poprawnie zarejestrowane w istniejącej puli hostów. Można bezpiecznie zignorować te błędy lub można uniknąć błędów, uruchamiając wszystkie maszyny wirtualne hosta sesji w istniejącej puli hostów przed rozpoczęciem procesu wdrażania.

## <a name="redeploy-from-azure"></a>Ponowne wdrożenie z platformy Azure

Jeśli utworzono już maszyny wirtualne z pulą hosta i hosta sesji przy użyciu [oferty portalu Azure Marketplace](./create-host-pools-azure-marketplace.md) lub szablonu Usługi Azure Resource Manager firmy [GitHub,](./create-host-pools-arm-template.md)można ponownie wdrożyć ten sam szablon z witryny Azure portal. Ponowne wdrożenie szablonu powoduje automatyczne ponowne wprowadzenie wszystkich informacji wprowadzonych do oryginalnego szablonu, z wyjątkiem haseł.

Poniżej opisano, jak ponownie wdrożyć szablon usługi Azure Resource Manager, aby rozwinąć pulę hostów:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Na pasku wyszukiwania u góry witryny Azure portal wyszukaj **grupy zasobów** i wybierz element w obszarze **Usługi**.
3. Znajdź i wybierz grupę zasobów utworzoną podczas tworzenia puli hostów.
4. W panelu po lewej stronie przeglądarki wybierz pozycję **Wdrożenia**.
5. Wybierz odpowiednie wdrożenie dla procesu tworzenia puli hostów:
     - Jeśli oryginalna pula hostów została utworzona z ofertą portalu Azure Marketplace, wybierz wdrożenie, zaczynając od **rds.wvd-provision-host-pool**.
     - Jeśli oryginalna pula hostów została utworzona z szablonem Usługi GitHub Azure Resource Manager, wybierz wdrożenie o nazwie **Microsoft.Template**.
6. Wybierz **pozycję Ponowne rozmieszczanie**.
     
     >[!NOTE]
     >Jeśli szablon nie zostanie automatycznie ponownie wdrożony po wybraniu opcji **Ponowne wdrażanie,** wybierz **pozycję Szablon** w panelu po lewej stronie przeglądarki, a następnie wybierz pozycję **Wdrażanie**.

7. Wybierz grupę zasobów zawierającą maszyny wirtualne hosta bieżącej sesji w istniejącej puli hostów.
     
     >[!NOTE]
     >Jeśli zostanie wyświetlony błąd informujący o wybraniu innej grupy zasobów, nawet jeśli wprowadzona grupa jest poprawna, wybierz inną grupę zasobów, a następnie wybierz oryginalną grupę zasobów.

8. Wprowadź następujący adres URL *_artifactsLocation:*`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Wprowadź nową całkowitą liczbę hostów sesji, które chcesz wprowadzić w *rdsh liczba wystąpień*. Jeśli na przykład powiększasz pulę hostów z pięciu hostów sesji do ośmiu, wprowadź **8**.
10. Wprowadź to samo istniejące hasło domeny, które zostało użyte dla istniejącej nazwy UPN domeny. Nie zmieniaj nazwy użytkownika, ponieważ spowoduje to błąd po uruchomieniu szablonu.
11. Wprowadź to samo hasło administratora dzierżawy używane dla identyfikatora użytkownika lub aplikacji wprowadzonego dla *identyfikatora upn administratora dzierżawy lub identyfikatora aplikacji*. Po raz kolejny nie zmieniaj nazwy użytkownika.
12. Wypełnij przesyłanie, aby rozszerzyć pulę hostów.

## <a name="run-the-azure-marketplace-offering"></a>Uruchamianie oferty portalu Azure Marketplace

Postępuj zgodnie z instrukcjami w [tworzenie puli hostów przy użyciu portalu Azure Marketplace,](./create-host-pools-azure-marketplace.md) dopóki nie dojdziesz [do programu Uruchom usługę Azure Marketplace oferującą aprowizowanie nowej puli hostów.](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool) Po dojście do tego punktu należy wprowadzić następujące informacje dla każdej karty:

### <a name="basics"></a>Podstawy

Wszystkie wartości w tej sekcji powinny być zgodne z wartościami podanymi podczas pierwszego utworzenia maszyn wirtualnych hosta i hosta sesji, z wyjątkiem *domyślnych użytkowników komputerów stacjonarnych:*

1.    W przypadku *subskrypcji*wybierz subskrypcję, w której po raz pierwszy utworzono pulę hostów.
2.    W przypadku *grupy zasobów*wybierz tę samą grupę zasobów, w której znajdują się istniejące maszyny wirtualne hosta puly hosta.
3.    W przypadku *regionu*wybierz ten sam region, w którym znajdują się istniejące maszyny wirtualne hosta puly hosta.
4.    W przypadku *nazwy hostpool*wprowadź nazwę istniejącej puli hostów.
5.    W przypadku *typu pulpitu*wybierz typ pulpitu odpowiadający istniejącej puli hostów.
6.    W przypadku *domyślnych użytkowników komputerów stacjonarnych*wprowadź listę dodatkowych użytkowników, którzy mają zalogować się do klientów pulpitu wirtualnego systemu Windows i uzyskać dostęp do pulpitu po zakończeniu oferowania usługi Azure Marketplace. Na przykład, jeśli chcesz user3@contoso.com user4@contoso.com przypisać i user3@contoso.comuser4@contoso.comuzyskać dostęp, wprowadź , .
7.    **Wybierz dalej: Konfigurowanie maszyny wirtualnej**.

>[!NOTE]
>Z wyjątkiem *domyślnych użytkowników komputerów stacjonarnych,* wszystkie pola muszą być dokładnie zgodne z tym, co zostało skonfigurowane w istniejącej puli hostów. Jeśli istnieje niezgodność, która spowoduje nową pulę hosta.

### <a name="configure-virtual-machines"></a>Konfigurowanie maszyn wirtualnych

Wszystkie wartości parametrów w tej sekcji powinny być zgodne z tym, co podano podczas pierwszego utworzenia maszyn wirtualnych hosta i hosta sesji, z wyjątkiem całkowitej liczby maszyn wirtualnych. Liczba wprowadzonych maszyn wirtualnych będzie liczbą maszyn wirtualnych w rozszerzonej puli hostów:

1. Wybierz rozmiar maszyny Wirtualnej, który odpowiada istniejącym maszynom wirtualnym hosta sesji.
    
    >[!NOTE]
    >Jeśli określony rozmiar maszyny Wirtualnej, którego szukasz, nie jest wyświetlany w selektorze rozmiaru maszyny Wirtualnej, to dlatego, że nie mamy jeszcze dołączane do narzędzia portalu Azure Marketplace. Aby zażądać rozmiaru maszyny Wirtualnej, utwórz żądanie lub prześlij żądanie w górę na [forum użytkownika pulpitu wirtualnego systemu Windows](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Dostosuj parametry *Profil użycia*, Całkowita liczba *użytkowników*i Liczba *maszyn wirtualnych,* aby wybrać całkowitą liczbę hostów sesji, które mają mieć w puli hostów. Na przykład jeśli rozszerzasz pulę hostów z pięciu hostów sesji do ośmiu, skonfiguruj te opcje, aby uzyskać dostęp do 8 maszyn wirtualnych.
3. Wprowadź prefiks nazw maszyn wirtualnych. Na przykład jeśli wprowadzisz nazwę "prefiks", maszyny wirtualne będą nazywane "prefiks-0", "prefiks-1" i tak dalej.
4. **Wybierz dalej : Ustawienia maszyny wirtualnej**.

### <a name="virtual-machine-settings"></a>Ustawienia maszyny wirtualnej

Wszystkie wartości parametrów w tej sekcji powinny być zgodne z tym, co podano podczas pierwszego utworzenia maszyn wirtualnych hosta i hosta sesji:

1. W przypadku *źródła obrazu* i wersji *systemu operacyjnego obrazu*wprowadź te same informacje, które zostały podane podczas pierwszego utworzenia puli hostów.
2. W przypadku *domeny usługi AD dołącz do nazwy UPN* i skojarzonych haseł wprowadź te same informacje, które zostały podane podczas pierwszego utworzenia puli hostów w celu przyłączenia maszyn wirtualnych do domeny usługi Active Directory. Te poświadczenia będą używane do tworzenia konta lokalnego na maszynach wirtualnych. Możesz zresetować te konta lokalne, aby później zmienić swoje poświadczenia.
3. W przypadku informacji o sieci wirtualnej wybierz tę samą sieć wirtualną i podsieć, w której znajdują się istniejące maszyny wirtualne hosta hosta.
4. Wybierz **dalej : Konfigurowanie informacji pulpitu wirtualnego systemu Windows**.

### <a name="windows-virtual-desktop-information"></a>Informacje o pulpicie wirtualnym systemu Windows

Wszystkie wartości parametrów w tej sekcji powinny być zgodne z tym, co podano podczas pierwszego utworzenia maszyn wirtualnych hosta i hosta sesji:

1. W przypadku *nazwy grupy dzierżawy pulpitu wirtualnego systemu Windows*wprowadź nazwę grupy dzierżawy zawierającej dzierżawę. Pozostaw go jako wartość domyślną, chyba że podano określoną nazwę grupy dzierżawy.
2. W przypadku *nazwy dzierżawy pulpitu wirtualnego systemu Windows*wprowadź nazwę dzierżawy, w której będzie ona tworzyć tę pulę hosta.
3. Określ te same poświadczenia, które zostały użyte podczas pierwszego utworzenia maszyn wirtualnych hosta i hosta sesji. Jeśli używasz jednostki usługi, wprowadź identyfikator wystąpienia usługi Azure Active Directory, w którym znajduje się jednostka usługi.
4. **Wybierz dalej : Recenzja + utwórz**.

## <a name="run-the-github-azure-resource-manager-template"></a>Uruchamianie szablonu usługi GitHub Azure Resource Manager

Postępuj zgodnie z instrukcjami w [uruchom szablon usługi Azure Resource Manager do inicjowania obsługi administracyjnej nowej puli hostów](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) i podać wszystkie te same wartości parametrów z wyjątkiem liczby *wystąpień Rdsh*. Wprowadź liczbę maszyn wirtualnych hosta sesji, które chcesz w puli hostów po uruchomieniu szablonu. Jeśli na przykład powiększasz pulę hostów z pięciu hostów sesji do ośmiu, wprowadź **8**.

## <a name="next-steps"></a>Następne kroki

Po rozszerzeniu istniejącej puli hostów można zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować go w ramach sesji użytkownika. Można połączyć się z sesją z dowolnym z następujących klientów:

- [Łączenie się z klientem klasycznym systemu Windows](./connect-windows-7-and-10.md)
- [Łączenie się z klientem internetowym](./connect-web.md)
- [Łączenie się z klientem systemu Android](./connect-android.md)
- [Nawiązywanie połączenia z klientem systemu macOS](./connect-macos.md)
- [Nawiązywanie połączenia z klientem systemu iOS](./connect-ios.md)
