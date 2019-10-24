---
title: pakiet Enterprise Security z Azure Active Directory w usłudze HDInsight
description: Dowiedz się, jak skonfigurować i skonfigurować klaster pakiet Enterprise Security usługi HDInsight przy użyciu Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 10/02/2019
ms.openlocfilehash: 448b2674aa6021107d138bc0d91f1bda399eb4a6
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755897"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>pakiet Enterprise Security konfiguracji z Azure Active Directory Domain Services w usłudze HDInsight

Klastry pakiet Enterprise Security (ESP) zapewniają dostęp przez wiele użytkowników do klastrów usługi Azure HDInsight. Klastry usługi HDInsight z protokołem ESP są połączone z domeną, dzięki czemu użytkownicy domeny mogą korzystać z poświadczeń domeny w celu uwierzytelniania w klastrach i uruchamiania zadań Big Data.

W tym artykule dowiesz się, jak skonfigurować klaster usługi HDInsight za pomocą protokołu ESP przy użyciu Azure Active Directory Domain Services (Azure AD-DS).

> [!NOTE]  
> Protokół ESP jest ogólnie dostępny w usłudze HDInsight 3,6 i 4,0 dla typów klastrów: Apache Spark, Interactive, Hadoop i HBase. ESP dla typu klastra Apache Kafka jest w wersji zapoznawczej z najlepszą obsługą. Klastry ESP utworzone przed datą "ESP" (1 października 2018) nie są obsługiwane.

## <a name="enable-azure-ad-ds"></a>Włączanie usługi Azure AD — DS

> [!NOTE]  
> Tylko Administratorzy dzierżawy mają uprawnienia do włączania usługi Azure AD — DS. Jeśli magazyn klastra jest Azure Data Lake Storage (ADLS) Gen1 lub Gen2, należy wyłączyć Multi-Factor Authentication (MFA) tylko dla użytkowników, którzy będą musieli uzyskać dostęp do klastra przy użyciu podstawowych uwierzytelnień Kerberos. Za pomocą [zaufanych adresów](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) IP lub [dostępu warunkowego](../../active-directory/conditional-access/overview.md) można wyłączyć uwierzytelnianie wieloskładnikowe dla określonych użytkowników tylko wtedy, gdy uzyskują dostęp do zakresu adresów IP sieci wirtualnej klastra usługi HDInsight. W przypadku korzystania z dostępu warunkowego upewnij się, że punkt końcowy usługi AD jest włączony w sieci wirtualnej HDInsight.
>
> Jeśli magazyn klastra to Azure Blob Storage (WASB), nie należy wyłączać usługi MFA.

Aby można było utworzyć klaster usługi HDInsight przy użyciu ESP, Włącz AzureAD-DS. Aby uzyskać więcej informacji, zobacz [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md). 

Gdy usługa Azure AD-DS jest włączona, wszyscy użytkownicy i obiekty domyślnie rozpoczynają synchronizację z Azure Active Directory (AAD) do usługi Azure AD DS. Długość operacji synchronizacji zależy od liczby obiektów w usłudze Azure AD. Synchronizacja może potrwać kilka dni dla setek tysięcy obiektów. 

Nazwa domeny, która jest używana z usługą Azure AD — nie może mieć więcej niż 39 znaków, do pracy z usługą HDInsight.

Można synchronizować tylko te grupy, które wymagają dostępu do klastrów usługi HDInsight. Ta opcja synchronizowania tylko określonych grup jest nazywana *synchronizacją w zakresie*. Aby uzyskać instrukcje [, zobacz Konfigurowanie synchronizacji z zakresem z usługi Azure AD w domenie zarządzanej](../../active-directory-domain-services/scoped-synchronization.md) .

Podczas włączania bezpiecznego protokołu LDAP należy umieścić nazwę domeny w polu Nazwa podmiotu i Alternatywna nazwa podmiotu w certyfikacie. Na przykład jeśli nazwa domeny to *contoso100.onmicrosoft.com*, upewnij się, że w nazwie podmiotu certyfikatu i alternatywnej nazwie podmiotu istnieje dokładna nazwa. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej usługi AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md). Poniżej znajduje się przykład tworzenia certyfikatu z podpisem własnym i nazwy domeny (*contoso100.onmicrosoft.com*) w obu nazwach podmiotów i dnsname (nazwa podmiotu):

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Sprawdź stan kondycji usługi Azure AD — DS
Aby wyświetlić stan kondycji Azure Active Directory Domain Services, wybierz pozycję **kondycja** w obszarze **Zarządzaj** kategorią. Upewnij się, że stan usługi Azure AD — DS jest zielony (uruchomiony) i że synchronizacja została ukończona.

![Azure Active Directory Domain Services kondycji](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Tworzenie i Autoryzowanie tożsamości zarządzanej

**Tożsamość zarządzana przypisana przez użytkownika** służy do upraszczania i zabezpieczania operacji usług domenowych. Po przypisaniu roli współautor usług domenowych w usłudze HDInsight do zarządzanej tożsamości można odczytywać, tworzyć, modyfikować i usuwać operacje usług domenowych. Niektóre operacje usług domenowych, takie jak tworzenie jednostek organizacyjnych i jednostki usługi, są zbędne dla pakiet Enterprise Security HDInsight. Tożsamości zarządzane można tworzyć w dowolnej subskrypcji. Aby uzyskać więcej informacji na temat ogólnych tożsamości zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](../hdinsight-managed-identities.md).

Aby skonfigurować klastry ESP, Utwórz tożsamość zarządzaną przypisaną przez użytkownika, jeśli jeszcze jej nie masz. Aby uzyskać instrukcje, zobacz [Tworzenie, wyświetlanie, usuwanie lub przypisywanie roli do zarządzanej tożsamości przypisanej Azure Portal przez użytkownika](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) . Następnie przypisz rolę **współautor usług domenowych** w usłudze HDInsight do tożsamości zarządzanej w usłudze Azure AD — kontrola dostępu w usłudze ds (AAD-ds uprawnienia administratora są wymagane do wykonania tego przypisania roli).

![Azure Active Directory Domain Services kontroli dostępu](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Przypisanie roli **współautor usług domenowych** w usłudze HDInsight zapewnia, że ta tożsamość ma odpowiedni (w imieniu) dostęp do wykonywania operacji usług domenowych, takich jak tworzenie jednostek organizacyjnych, usuwanie jednostek organizacyjnych itp. w DOMENIE usługi AAD.

Po utworzeniu tożsamości zarządzanej i otrzymaniu odpowiedniej roli administrator usługi AAD-DS może skonfigurować, kto może korzystać z tej tożsamości zarządzanej. Aby skonfigurować użytkowników dla tożsamości zarządzanej, administrator powinien wybrać tożsamość zarządzaną w portalu, a następnie kliknąć pozycję **Access Control (IAM)** w obszarze **Przegląd**. Następnie po prawej stronie Przypisz rolę **operatora tożsamości zarządzanej** do użytkowników lub grup, które chcą tworzyć klastry usługi HDInsight ESP. Na przykład administrator usługi AAD-DS może przypisać tę rolę do grupy **MarketingTeam** dla tożsamości zarządzanej **sjmsi** , jak pokazano na poniższej ilustracji. Dzięki temu odpowiednie osoby w organizacji mają dostęp do korzystania z tej tożsamości zarządzanej na potrzeby tworzenia klastrów ESP.

![Przypisanie roli operatora tożsamości zarządzanego przez usługi HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

> [!NOTE]  
> Usługę Azure AD-DS należy wdrożyć w sieci wirtualnej opartej na Azure Resource Manager. Klasyczne sieci wirtualne nie są obsługiwane w usłudze Azure AD — DS. Aby uzyskać więcej informacji, zobacz [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Po włączeniu usługi Azure AD-DS lokalny serwer usługi nazw domen (DNS) jest uruchamiany na Virtual Machines usługi AD. Skonfiguruj Virtual Network usługi Azure AD — DS (VNET), aby korzystać z tych niestandardowych serwerów DNS. Aby zlokalizować odpowiednie adresy IP, wybierz pozycję **Właściwości** w kategorii **Zarządzanie** i Sprawdź adresy IP wymienione pod **adresem IP na Virtual Network**.

![Lokalizowanie adresów IP dla lokalnych serwerów DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Zmień konfigurację serwerów DNS w sieci wirtualnej usługi Azure AD-DS, aby użyć tych niestandardowych adresów IP, wybierając pozycję **serwery DNS** w kategorii **Ustawienia** . Następnie kliknij przycisk radiowy obok pozycji **niestandardowy**, wprowadź pierwszy adres IP w polu tekstowym poniżej i kliknij przycisk **Zapisz**. Dodaj dodatkowe adresy IP, korzystając z tych samych kroków.

![Aktualizowanie konfiguracji DNS sieci wirtualnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

W tej samej sieci wirtualnej platformy Azure można łatwiej umieścić zarówno wystąpienie usługi Azure AD — usługi domenowe, jak i klaster usługi HDInsight. Jeśli planujesz używać różnych sieci wirtualnych, musisz nawiązać połączenie równorzędne z tymi sieciami wirtualnymi, aby kontroler domeny był widoczny dla maszyn wirtualnych HDI. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../../virtual-network/virtual-network-peering-overview.md). 

Po sieci wirtualnych komunikacji równorzędnej Skonfiguruj sieć wirtualną usługi HDInsight pod kątem używania niestandardowego serwera DNS i wprowadź prywatne adresy IP w usłudze Azure AD — jako adresy serwerów DNS. Gdy oba sieci wirtualnych używają tych samych serwerów DNS, nazwa domeny niestandardowej zostanie rozwiązany z właściwym adresem IP i będzie dostępna z usługi HDInsight. Jeśli na przykład nazwa domeny jest `contoso.com` następnie po tym kroku, `ping contoso.com` powinna zostać rozwiązana z właściwym adresem IP usługi Azure AD — DS.

![Konfigurowanie niestandardowych serwerów DNS dla równorzędnej sieci wirtualnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Jeśli w podsieci usługi HDInsight są używane reguły grup zabezpieczeń sieci (sieciowej grupy zabezpieczeń), należy zezwolić na [wymagane adresy IP](../hdinsight-management-ip-addresses.md) dla ruchu przychodzącego i wychodzącego.

**Aby sprawdzić** , czy sieć została prawidłowo skonfigurowana, Dołącz maszynę wirtualną z systemem Windows do sieci wirtualnej/podsieci usługi HDInsight, a następnie Wyślij polecenie ping do nazwy domeny (należy rozwiązać adres IP), a następnie uruchom program **Ldp. exe** , aby uzyskać dostęp do domeny usługi Azure AD DS. Następnie **Przyłącz tę maszynę wirtualną z systemem Windows do domeny, aby upewnić** się, że wszystkie wymagane wywołania RPC powiodło się między klientem a serwerem. Za pomocą narzędzia **nslookup** można także potwierdzić dostęp sieciowy do konta magazynu lub dowolnej zewnętrznej bazy danych, która może być używana (na przykład zewnętrzne magazyn metadanych Hive lub Ranger DB).
Należy upewnić się, że wszystkie [wymagane porty](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) są listy dozwolonych w regułach sieciowej grupy zabezpieczeń usługi AAD-DS, jeśli usługa AAD-DS jest zabezpieczona przez sieciowej grupy zabezpieczeń. Jeśli łączenie domeny z maszyną wirtualną z systemem Windows powiedzie się, możesz przejść do następnego kroku i utworzyć klastry ESP.

## <a name="create-a-hdinsight-cluster-with-esp"></a>Tworzenie klastra usługi HDInsight przy użyciu protokołu ESP

Po poprawnym skonfigurowaniu poprzednich kroków następnym krokiem jest utworzenie klastra usługi HDInsight z włączonym protokołem ESP. Podczas tworzenia klastra usługi HDInsight można włączyć pakiet Enterprise Security na karcie **zabezpieczenia i sieć** . Jeśli wolisz używać szablonu Azure Resource Manager do wdrożenia, użyj środowiska portalu raz i Pobierz wstępnie wypełniony szablon na stronie **Recenzja + tworzenie** do użycia w przyszłości.

> [!NOTE]  
> Pierwsze sześć znaków nazw klastrów ESP musi być unikatowe w danym środowisku. Na przykład jeśli masz wiele klastrów ESP w różnych sieci wirtualnych, należy wybrać nazwę convension, która zapewnia unikalność pierwszych sześciu znaków w nazwach klastra.

![Weryfikacja domeny pakietu zabezpieczeń usługi Azure HDInsight Enterprise Security](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Po włączeniu protokołu ESP typowe błędne konfiguracje związane z usługą Azure AD — DS będą automatycznie wykrywane i weryfikowane. Po rozwiązaniu tych błędów można przejść do następnego kroku:

![Nie można zweryfikować domeny pakietu zabezpieczeń usługi Azure HDInsight Enterprise Security](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Podczas tworzenia klastra usługi HDInsight przy użyciu protokołu ESP należy podać następujące parametry:

- **Użytkownik administrator klastra**: Wybierz administratora dla klastra ze zsynchronizowanej usługi Azure AD — DS. To konto domeny musi być już zsynchronizowane i dostępne w usłudze Azure AD — DS.

- **Grupy dostępu klastra**: grupy zabezpieczeń, których użytkownicy mają synchronizować i mają dostęp do klastra, powinny być dostępne w usłudze Azure AD — DS. Na przykład grupa HiveUsers. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy i Dodawanie członków w Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

- **Adres URL adresów LDAP**: przykład to `ldaps://contoso.com:636`.

Utworzoną tożsamość zarządzaną można wybrać z listy rozwijanej zarządzanej tożsamości przypisanej przez użytkownika podczas tworzenia nowego klastra.

![Tożsamość zarządzana w usłudze Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zasady Hive i uruchamiać zapytania programu Hive, zobacz [Konfigurowanie zasad Apache Hive dla klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-run-hive.md).
* Aby połączyć się z klastrami usługi HDInsight przy użyciu protokołu SSH, zobacz [Używanie protokołu SSH z Apache Hadoop opartych na systemie Linux w usłudze HDInsight z systemu Linux, UNIX lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
