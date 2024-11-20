<asp:TemplateField HeaderText="Unpaid Arear Wages">
    <HeaderTemplate>
        Unpaid Arear Wages <span style="color: red;">[f]</span>
    </HeaderTemplate>
    <ItemTemplate>
        <%# Eval("Unpaid_ArearWages") %>
    </ItemTemplate>
    <HeaderStyle HorizontalAlign="Left" />
    <ItemStyle HorizontalAlign="Left" />
</asp:TemplateField>
