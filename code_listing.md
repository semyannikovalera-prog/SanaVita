## Листинг программы

### FormEnter

```csharp
public partial class FormEnter : Form
{
    private string _typeUser = "";

    public FormEnter(string typeUser)
    {
        InitializeComponent();
        if (typeUser == "guest")
        {
            labelTitleApp.Text += " отдыхающих";
            _typeUser = "guest";
        }
        else if (typeUser == "admin")
        {
            labelTitleApp.Text += " администраторов";
            _typeUser = "admin";
        }
        else throw new Exception("Такого типа пользователя не существует. Должен быть один из [guest, admin]");
    }

    private void buttonEnter_Click(object sender, EventArgs e)
    {
        var formMain = new FormMain(_typeUser);
        formMain.Show();
    }

    private void buttonRegist_Click(object sender, EventArgs e)
    {
        Form formRegist;
        if (_typeUser == "guest") formRegist = new FormRegistGuest();
        else formRegist = new FormRegistAdmin();

        var resultRegist = formRegist.ShowDialog();
        if (resultRegist == DialogResult.OK)
        {
            formRegist.Close();
            this.Activate();
        }
    }
}
```

### FormMain

```csharp
public partial class FormMain : Form
{
    private string _typeUser = "";

    public FormMain(string typeUser)
    {
        InitializeComponent();
        _typeUser = typeUser;
        if (_typeUser == "admin")
        {
            this.buttonMySanatoriums.Enabled = true;
        }
    }

    private void markSanatorium_Click(object sender, EventArgs e)
    {
        var formInfo = new FormInfoSanatorium("Санаторий «Южный»");
        formInfo.Show();
    }

    private void buttonBooks_Click(object sender, EventArgs e)
    {
        new FormBooks().Show();
    }

    private void buttonProfile_Click(object sender, EventArgs e)
    {
        Form form;
        if (_typeUser == "guest") form = new FormProfileGuest();
        else form = new FormProfileAdmin();
        form.Show();
    }

    private void buttonPayQR_Click(object sender, EventArgs e)
    {
        new FormPayQR().Show();
    }

    private void buttonHistory_Click(object sender, EventArgs e)
    {
        new FormHistory().Show();
    }

    private void buttonMySanatoriums_Click(object sender, EventArgs e)
    {
        new FormMySanatoriums().Show();
    }
}
```

### FormInfoSanatorium

```csharp
public partial class FormInfoSanatorium : Form
{
    private string _name = "";

    public FormInfoSanatorium(string name)
    {
        InitializeComponent();
        _name = name;
    }

    private void buttonBook_Click(object sender, EventArgs e)
    {
        var formBook = new FormBook();
        formBook.ShowDialog();
        if (formBook.DialogResult == DialogResult.OK)
        {
            formBook.Close();
            this.Activate();
        }
    }

    private void buttonPay_Click(object sender, EventArgs e)
    {
        var formPay = new FormPay();
        formPay.ShowDialog();
        if (formPay.DialogResult == DialogResult.OK)
        {
            formPay.Close();
            this.Activate();
        }
    }
}
```

### FormBook

```csharp
public partial class FormBook : Form
{
    private const decimal PricePerDay = 4200m;
    private decimal _programCost = 7000m;

    public FormBook()
    {
        InitializeComponent();
        RecalculateTotal();
    }

    private void RecalculateTotal()
    {
        int days = (dateTimePickerTo.Value.Date - dateTimePickerFrom.Value.Date).Days;
        if (days <= 0)
        {
            labelTotal.Text = "Итого: 0 руб";
            buttonBook.Enabled = false;
            return;
        }
        decimal total = days * PricePerDay + _programCost;
        labelTotal.Text = $"Итого: {total:N0} руб";
        buttonBook.Enabled = true;
    }

    private void dateTimePicker_ValueChanged(object sender, EventArgs e)
    {
        RecalculateTotal();
    }

    private void buttonBook_Click(object sender, EventArgs e)
    {
        MessageBox.Show("Номер забронирован", "Успех",
            MessageBoxButtons.OK, MessageBoxIcon.Information);
        this.DialogResult = DialogResult.OK;
    }
}
```

### FormCreateSanatorium

```csharp
public partial class FormCreateSanatorium : Form
{
    public FormCreateSanatorium()
    {
        InitializeComponent();
    }

    private void buttonEditMap_Click(object sender, EventArgs e)
    {
        var form = new FormEditMap();
        var res = form.ShowDialog();
        if (res == DialogResult.Yes)
        {
            form.Close();
            Controls.Remove(this.labelNotMap);
        }
    }

    private void buttonCreate_Click(object sender, EventArgs e)
    {
        this.Close();
    }
}
```

### FormEditMap

```csharp
public partial class FormEditMap : Form
{
    public FormEditMap()
    {
        InitializeComponent();
    }

    private void buttonSaveMap_Click(object sender, EventArgs e)
    {
        var res = MessageBox.Show("Сохранить схему?", "",
            MessageBoxButtons.YesNo, MessageBoxIcon.Question);
        if (res == DialogResult.Yes)
        {
            this.DialogResult = DialogResult.Yes;
        }
    }

    private void roomButton_Click(object sender, EventArgs e)
    {
        var res = MessageBox.Show("Удалить этот номер?", "",
            MessageBoxButtons.YesNo, MessageBoxIcon.Question);
        if (res == DialogResult.Yes)
        {
            Controls.Remove((Control)sender);
        }
        this.Activate();
    }

    private void buttonRemoveAll_Click(object sender, EventArgs e)
    {
        var rooms = Controls.OfType<Button>()
                            .Where(b => b.Name == "roomCreated")
                            .ToList();
        foreach (var room in rooms) Controls.Remove(room);
    }
}
```
