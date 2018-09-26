

Po utworzeniu projektu aplikacji sieci web na platformie Azure można aprowizować maszynę wirtualną na platformie Azure. Następnie można skonfigurować maszynę wirtualną przy użyciu dodatkowego oprogramowania lub użyć maszyny wirtualnej do celów diagnostycznych lub debugowania.

Aby utworzyć maszynę wirtualną podczas tworzenia aplikacji sieci web, wykonaj następujące kroki:

1. W programie Visual Studio, kliknij przycisk **pliku** > **nowy** > **projektu** > **Web**, a następnie wybierz pozycję **Aplikacji sieci Web ASP.NET** (w obszarze **Visual C#** lub **języka Visual Basic** węzłów).
2. W **nowy projekt ASP.NET** okno dialogowe, wybierz typ aplikacji sieci web, które chcesz, a w sekcji Azure okna dialogowego (w prawym dolnym rogu), upewnij się, że **Hostuj w chmurze** jest pole wyboru wybrane (to pole wyboru jest oznaczona etykietą **Utwórz zasoby zdalne** w niektórych urządzeniach).
   
    ![][0]
3. Na przykład w obszarze Microsoft Azure, na liście rozwijanej wybierz **maszyny wirtualnej (v1)**, a następnie kliknij przycisk **OK** przycisku.
4. Zaloguj się do platformy Azure po wyświetleniu monitu. **Utwórz maszynę wirtualną** pojawi się okno dialogowe.
   
    ![][2]
5. W **nazwy DNS** wprowadź nazwę dla maszyny wirtualnej. Nazwa DNS musi być unikatowa na platformie Azure. Jeśli wprowadzona nazwa jest niedostępna, pojawi się czerwony wykrzyknik.
6. W **obraz** listy, możesz wybrać obraz, którego chcesz utworzyć maszynę wirtualną na. Można wybrać obrazy maszyn wirtualnych platformy Azure standard lub obraz przekazanego do platformy Azure.
7. Pozostaw **Włącz usługi IIS i narzędzia Web Deploy** pole wyboru jest zaznaczone, chyba że zamierzasz zainstalować różnych serwerów sieci web. Nie można opublikować z programu Visual Studio po wyłączeniu narzędzia Web Deploy. Usługi IIS i narzędzia Web Deploy można dodać do dowolnego spakowanych obrazy systemu Windows Server, w tym własne niestandardowe obrazy.
8. W **rozmiar** wybierz rozmiar maszyny wirtualnej.
9. Określ poświadczenia logowania dla tej maszyny wirtualnej. Zanotuj je, ponieważ konieczne będzie ich dostęp do komputera za pośrednictwem usług pulpitu zdalnego.
10. W **lokalizacji** listy, wybierz region, aby hostować maszynę wirtualną.
11. Kliknij przycisk **OK** przycisk, aby rozpocząć tworzenie maszyny wirtualnej. Możesz śledzić postęp operacji w **dane wyjściowe** okna.
    
    ![][3]
12. Gdy maszyna wirtualna jest gotowa, opublikowane skrypty są tworzone w **PublishScripts** węzła w rozwiązaniu. Opublikowane skrypt jest uruchamiany i aprowizuje maszyny wirtualnej na platformie Azure. **Dane wyjściowe** okna wyświetlany jest stan. Skrypt wykonuje następujące czynności, aby skonfigurować maszynę wirtualną:
    
    * Tworzy maszynę wirtualną, jeśli jeszcze nie istnieje.
    * Tworzy konto magazynu o nazwie, która rozpoczyna się od `devtest`, ale tylko wtedy, gdy nie ma już konto magazynu w wybranym regionie.
    * Tworzy usługę w chmurze jako kontener dla maszyny wirtualnej oraz roli sieci web dla aplikacji sieci web.
    * Konfiguruje narzędzie Web Deploy, na maszynie wirtualnej.
    * Umożliwia skonfigurowanie usług IIS i platformy ASP.NET na maszynie wirtualnej.
    
    ![][4]
13. (Opcjonalnie) Możesz połączyć do nowej maszyny wirtualnej. W **Eksploratora serwera**, rozwiń węzeł **maszyn wirtualnych** węzła, wybierz węzeł dla maszyny wirtualnej został utworzony, a jego menu skrótów, wybierz pozycję **Połącz przy użyciu pulpitu zdalnego**. Możesz również w **programu Cloud Explorer** możesz wybrać **Otwórz w portalu** menu skrótów i nawiąż połączenie z maszyną wirtualną.
    
    ![][5]

## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz dostosować opublikowane skrypty, został utworzony, zapoznaj się z bardziej szczegółowe informacje o [za pomocą skryptów programu PowerShell Windows Publikuj deweloperskim i testowym](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
