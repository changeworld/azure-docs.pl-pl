---
title: Przewodnik Szybki Start — tworzenie strefy DNS platformy Azure i zarejestrować przy użyciu witryny Azure portal
description: Ten szczegółowy przewodnik Szybki start przedstawia tworzenie strefy usługi Azure DNS i rekordu przy użyciu witryny Azure Portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: feb46114b3cf1b04e6a181f84bcdc41c17f1c0ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563293"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Szybki start: Tworzenie usługi Azure strefy i rekordu DNS przy użyciu witryny Azure portal

Możesz skonfigurować usługę Azure DNS do rozpoznawania nazw hostów w domenie publicznej. Na przykład, jeśli zakupiono *contoso.xyz* nazwy domeny z Rejestratora nazw domen, możesz skonfigurować usługę Azure DNS do hosta *contoso.xyz* domeny i rozwiązywanie *www.contoso.xyz* adres IP serwera sieci web lub aplikacji sieci web.

Podczas pracy z tym przewodnikiem Szybki start utworzysz domenę testową, a następnie rekord adresu powodujący rozpoznawanie nazwy *www* jako adresu IP *10.10.10.10*.

>[!IMPORTANT]
>Wszystkie nazwy i adresy IP w tym przewodniku Szybki start są przykładowe i nie reprezentują rzeczywistych scenariuszy.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

W przypadku wszystkich kroków wykonywanych w portalu należy się zalogować do [witryny Azure Portal](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Tworzenie strefy DNS

Strefa DNS zawiera wpisy DNS dla domeny. Aby rozpocząć hostowanie domeny w usłudze Azure DNS, należy utworzyć strefę DNS dla tej nazwy domeny. 

**Aby utworzyć strefę DNS:**

1. W lewym górnym rogu wybierz pozycję **Utwórz zasób**, a następnie **Sieć** i **Strefa DNS**.

1. Na stronie **Tworzenie strefy DNS** wpisz lub wybierz następujące wartości:

   - **Nazwa**: wpisz *contoso.xyz* jako przykład w tym przewodniku Szybki start. Nazwa strefy DNS może być dowolną wartością, która nie jest już skonfigurowana na serwerach usługi Azure DNS. W rzeczywistym scenariuszu tą wartością byłaby domena kupiona u rejestratora nazw domen.
   - **Grupa zasobów**: Wybierz **Utwórz nową**, wprowadź *MyResourceGroup*i wybierz **OK**. Nazwa grupy zasobów musi być unikatowa w ramach subskrypcji platformy Azure. 

1. Wybierz pozycję **Utwórz**.

   ![Strefa DNS](./media/dns-getstarted-portal/openzone650.png)

Tworzenie strefy może potrwać kilka minut.

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Wpisy, czyli rekordy, DNS tworzy się dla domeny w strefie DNS. Utwórz nowy rekordu adresu, czyli rekord „A”, aby umożliwić rozpoznawanie nazwy hosta jako adresu IPv4.

**Aby utworzyć rekord „A”:**

1. W witrynie Azure portal w obszarze **wszystkie zasoby**, otwórz **contoso.xyz** strefę DNS **MyResourceGroup** grupy zasobów. Wartość *contoso.xyz* można wpisać w polu **Filtruj według nazwy**, aby łatwiej znaleźć strefę.

1. W górnej części strony **Strefa DNS** wybierz pozycję **+ Zestaw rekordów**.

1. Na stronie **Dodawanie zestawu rekordów** wpisz lub wybierz następujące wartości:

   - **Nazwa**: wpisz wartość *www*. Nazwa rekordu to nazwa hosta, która ma być rozpoznawana jako określony adres IP.
   - **Typ**: wybierz pozycję **A**. Rekordy „A” są najczęściej używane, ale istnieją też inne typy rekordów: dla serwerów poczty e-mail („MX”), adresów IPv6 („AAAA”) itd. 
   - **Czas wygaśnięcia**: wpisz *1*. *Czas wygaśnięcia* żądania DNS określa, jak długo serwery i klienci DNS mogą buforować odpowiedź.
   - **Jednostka czasu wygaśnięcia**: wybierz pozycję **Godziny**. Jest to jednostka czasu dla wartości **Czas wygaśnięcia**. 
   - **Adres IP**: w przypadku tego przykładu wpisz *10.10.10.10*. Ta wartość to adres IP rozpoznawany w przypadku nazwy rekordu. W rzeczywistym scenariuszu byłby to na przykład publiczny adres IP serwera internetowego.

Ponieważ ten przewodnik Szybki Start jest tylko na potrzeby szybkiego testowania, nie ma potrzeby konfigurowania serwerów nazw usługi Azure DNS u rejestratora nazw domen. Przy użyciu domeny rzeczywistej produkcji należy każdy w Internecie, aby rozpoznać nazwy hosta, aby nawiązać połączenie z serwera sieci web lub aplikacji. Odwiedzisz rejestratora nazw domen, aby zastąpić rekordy serwera nazw serwerami nazw usługi Azure DNS. Więcej informacji znajduje się artykule [Samouczek: hostowanie własnej domeny w usłudze Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Testowanie rozpoznawania nazw

Po utworzeniu testowej strefy DNS z rekordem „A” możesz przetestować rozpoznawanie nazw za pomocą narzędzia o nazwie *nslookup*. 

**Aby przetestować rozpoznawanie nazw DNS:**

1. W witrynie Azure portal w obszarze **wszystkie zasoby**, otwórz **contoso.xyz** strefę DNS **MyResourceGroup** grupy zasobów. Wartość *contoso.xyz* można wpisać w polu **Filtruj według nazwy**, aby łatwiej znaleźć strefę.

1. Skopiuj jedną z nazw serwerów nazw z listy serwerów nazw na stronie **Przegląd**. 

   ![strefa](./media/dns-getstarted-portal/viewzonens500.png)

1. Otwórz wiersz polecenia i uruchom następujące polecenie:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Na przykład:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Powinna zostać wyświetlona treść podobna do tej na następującym ekranie:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Nazwa hosta **www\.contoso.xyz** jest rozpoznawana jako **10.10.10.10**tak samo jak został skonfigurowany. Taki wynik potwierdza, że rozpoznawanie nazw działa poprawnie. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w tym przewodniku Szybki Start, usuń je, usuwając **MyResourceGroup** grupy zasobów. Otwórz **MyResourceGroup** zasobu, grupy i wybierz **Usuń grupę zasobów**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Create DNS records for a web app in a custom domain (Tworzenie rekordów DNS aplikacji internetowej w domenie niestandardowej)](./dns-web-sites-custom-domain.md)