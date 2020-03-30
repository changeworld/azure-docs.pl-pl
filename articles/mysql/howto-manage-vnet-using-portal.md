---
title: Zarządzanie punktami końcowymi sieci wirtualnej — witryna Azure portal — usługa Azure Database for MySQL
description: Tworzenie punktów końcowych i reguł usługi Azure Database dla sieci wirtualnej MySQL i zarządzanie nimi przy użyciu witryny Azure portal
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: fec85f7d87bbfe0039744b9ea63239083ff1c8a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063531"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Tworzenie i zarządzanie usługą Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL i reguł sieci wirtualnej przy użyciu portalu Azure
Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MySQL. Aby uzyskać omówienie usługi Azure Database dla punktów końcowych usługi sieci wirtualnej MySQL, w tym ograniczenia, zobacz [Usługa Azure Database dla punktów końcowych usługi sieci wirtualnej serwera MySQL](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach usługi Azure Database for MySQL.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnej, jeśli ruch przepływa przez wspólną bramę sieci wirtualnej z punktami końcowymi usługi i ma przepływ do elementu równorzędnego, utwórz regułę ACL/VNet, aby zezwolić maszynom wirtualnym platformy Azure w sieci wirtualnej bramy na dostęp do bazy danych platformy Azure dla serwera MySQL.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Tworzenie reguły sieci wirtualnej i włączanie punktów końcowych usługi w witrynie Azure portal

1. Na stronie serwera MySQL w obszarze Nagłówek Ustawienia kliknij pozycję **Zabezpieczenia połączeń,** aby otworzyć okienko Zabezpieczeń połączeń dla usługi Azure Database for MySQL. 

2. Upewnij się, że formant Zezwalaj na dostęp do usług platformy Azure jest ustawiony na **OFF**.

> [!Important]
> Jeśli pozostawisz formant ustawiony na WŁĄCZONY, serwer bazy danych MySQL platformy Azure zaakceptuje komunikację z dowolnej podsieci. Pozostawienie formantu ustawionego na ON może być nadmiernym dostępem z punktu widzenia zabezpieczeń. Funkcja punktu końcowego usługi sieci wirtualnej platformy Microsoft Azure, w koordynacji z funkcją reguły sieci wirtualnej usługi Azure Database for MySQL, razem może zmniejszyć obszar powierzchni zabezpieczeń.

3. Następnie kliknij **+ Dodawanie istniejącej sieci wirtualnej**. Jeśli nie masz istniejącej sieci wirtualnej, możesz kliknąć **+ Utwórz nową sieć wirtualną,** aby ją utworzyć. Zobacz [Szybki start: tworzenie sieci wirtualnej przy użyciu portalu Azure](../virtual-network/quick-create-portal.md)

   ![Portal Azure — kliknij pozycję Zabezpieczenia połączeń](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Wprowadź nazwę reguły sieci wirtualnej, wybierz subskrypcję, nazwę sieci wirtualnej i podsieci, a następnie kliknij przycisk **Włącz**. Spowoduje to automatyczne włącza punkty końcowe usługi sieci wirtualnej w podsieci przy użyciu tagu usługi **Microsoft.SQL.**

   ![Portal Azure — konfigurowanie sieci wirtualnej](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

   Punkty końcowe usługi mogą być konfigurowane w sieciach wirtualnych niezależnie przez użytkownika z dostępem do zapisu do sieci wirtualnej.
    
   Aby zabezpieczyć zasoby usługi platformy Azure do sieci wirtualnej, użytkownik musi mieć uprawnienia do "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.
    
   Dowiedz się więcej na temat [wbudowanych ról](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby usługi sieci wirtualnej i platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny znajdować się w tej samej dzierżawie usługi Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowanego dostawcy zasobów **Microsoft.Sql.** Aby uzyskać więcej informacji, zapoznaj się [z rejestracją menedżera zasobów][resource-manager-portal]

   > [!IMPORTANT]
   > Zdecydowanie zaleca się przeczytanie tego artykułu na temat konfiguracji punktu końcowego usługi i zagadnienia przed skonfigurowaniem punktów końcowych usługi. **Punkt końcowy usługi sieci wirtualnej:** [Punkt końcowy usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwy typu usługi Platformy Azure. Punkty końcowe usług sieci wirtualnej używają nazwy typu usługi **Microsoft.Sql**, która odnosi się do usługi Azure o nazwie baza danych SQL. Ten tag usługi ma również zastosowanie do usługi Azure SQL Database, Azure Database for PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania tagu usługi **Microsoft.Sql** do punktu końcowego usługi sieci wirtualnej konfiguruje ruch punktu końcowego usługi dla wszystkich usług usługi Azure Database, w tym usługi Azure SQL Database, azure database for PostgreSQL i Azure Database for MySQL serwerów w podsieci. 
   > 

5. Po włączeniu kliknij przycisk **OK,** a zobaczysz, że punkty końcowe usługi sieci wirtualnej są włączone wraz z regułą sieci wirtualnej.

   ![Włączone punkty końcowe usługi sieci wirtualnej i utworzona reguła sieci wirtualnej](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Następne kroki
- Podobnie można skrypt, aby [włączyć punkty końcowe usługi sieci wirtualnej i utworzyć regułę sieci wirtualnej dla usługi Azure Database dla MySQL przy użyciu interfejsu wiersza polecenia platformy Azure.](howto-manage-vnet-using-cli.md)
- Aby uzyskać pomoc dotyczącą łączenia się z usługą Azure Database dla serwera MySQL, zobacz [Biblioteki połączeń dla usługi Azure Database for MySQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md