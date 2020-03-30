---
title: Zarządzanie punktami końcowymi sieci wirtualnej — witryna Azure portal — usługa Azure Database for MariaDB
description: Tworzenie punktów końcowych i reguł usługi Azure Database dla sieci wirtualnej MariaDB i zarządzanie nimi przy użyciu portalu Azure Portal
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 61a8337536f55ceda9bef5b7eaa67a37644d2aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530601"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie usługą Azure Database dla punktów końcowych usługi mariadb i reguł sieci wirtualnej przy użyciu portalu Azure

Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MariaDB. Aby zapoznać się z omówieniem punktów końcowych usługi MariaDB dla sieci wirtualnej MariaDB, w tym ograniczeń, zobacz [Punkty końcowe usługi sieci wirtualnej serwera MariaDB dla serwera MariaDB](concepts-data-access-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach usługi Azure Database for MariaDB.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Tworzenie reguły sieci wirtualnej i włączanie punktów końcowych usługi

1. Na stronie serwera MariaDB w obszarze Nagłówek Ustawienia kliknij pozycję **Zabezpieczenia połączeń,** aby otworzyć okienko Zabezpieczeń połączeń dla usługi Azure Database for MariaDB.

2. Upewnij się, że formant Zezwalaj na dostęp do usług platformy Azure jest ustawiony na **OFF**.

> [!Important]
> Jeśli ustawisz go na WŁĄCZONE, serwer bazy danych Azure MariaDB akceptuje komunikację z dowolnej podsieci. Pozostawienie formantu ustawionego na ON może być nadmiernym dostępem z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi sieci wirtualnej platformy Microsoft Azure, w koordynacji z funkcją reguł sieci wirtualnej usługi Azure Database dla MariaDB, razem można zmniejszyć obszar powierzchni zabezpieczeń.

3. Następnie kliknij **+ Dodawanie istniejącej sieci wirtualnej**. Jeśli nie masz istniejącej sieci wirtualnej, możesz kliknąć **+ Utwórz nową sieć wirtualną,** aby ją utworzyć. Zobacz [Szybki start: tworzenie sieci wirtualnej przy użyciu portalu Azure](../virtual-network/quick-create-portal.md)

   ![Portal Azure — kliknij pozycję Zabezpieczenia połączeń](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, nazwę sieci wirtualnej i podsieci, a następnie kliknij przycisk **Włącz**. Spowoduje to automatyczne włącza punkty końcowe usługi sieci wirtualnej w podsieci przy użyciu tagu usługi **Microsoft.SQL.**

   ![Portal Azure — konfigurowanie sieci wirtualnej](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

   Punkty końcowe usługi mogą być konfigurowane w sieciach wirtualnych niezależnie przez użytkownika z dostępem do zapisu do sieci wirtualnej.
    
   Aby zabezpieczyć zasoby usługi platformy Azure do sieci wirtualnej, użytkownik musi mieć uprawnienia do "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.
    
   Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby usługi sieci wirtualnej i platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny znajdować się w tej samej dzierżawie usługi Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowanego dostawcy zasobów **Microsoft.Sql.** Aby uzyskać więcej informacji, zapoznaj się [z rejestracją menedżera zasobów][resource-manager-portal]

   > [!IMPORTANT]
   > Zdecydowanie zaleca się przeczytanie tego artykułu na temat konfiguracji punktu końcowego usługi i zagadnienia przed skonfigurowaniem punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** [Punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwy typu usługi Platformy Azure. Punkty końcowe usług sieci wirtualnej używają nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi Azure o nazwie baza danych SQL. Ten tag usługi ma również zastosowanie do usługi Azure SQL Database, Azure Database for MariaDB, PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania tagu usługi **Microsoft.Sql** do punktu końcowego usługi sieci wirtualnej konfiguruje ruch punktu końcowego usługi dla wszystkich usług usługi Azure Database, w tym usługi Azure SQL Database, azure database for PostgreSQL, Azure Database for MariaDB i Azure Database for MySQL serwerów w podsieci.
   > 

5. Po włączeniu kliknij przycisk **OK,** a zobaczysz, że punkty końcowe usługi sieci wirtualnej są włączone wraz z regułą sieci wirtualnej.

   ![Włączone punkty końcowe usługi sieci wirtualnej i utworzona reguła sieci wirtualnej](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [konfigurowaniu ssl w usłudze Azure Database for MariaDB](howto-configure-ssl.md)
- Podobnie można skrypt, aby [włączyć punkty końcowe usługi sieci wirtualnej i utworzyć regułę sieci wirtualnej dla usługi Azure Database dla MariaDB przy użyciu interfejsu wiersza polecenia platformy Azure.](howto-manage-vnet-cli.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md