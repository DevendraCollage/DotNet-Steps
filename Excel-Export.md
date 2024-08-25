# Steps for Export to Excel

## Exporting to Excel

The `ExportToExcel` method is used to export the data to an Excel file. The method is called on the `DataTable` object. Th method takes the following parameters:

## Step 1: Add the library named EPPlus from Nuget Package Manager

## Step 2: Create a Controller and Action Method to Display Data

```csharp
    // Method to list the Product on the view page
    public IActionResult ProductList()
    {
        string connectionString = this.configuration.GetConnectionString("ConnectionString");
        SqlConnection connection = new SqlConnection(connectionString);
        connection.Open();
        SqlCommand command = connection.CreateCommand();
        command.CommandType = CommandType.StoredProcedure;
        command.CommandText = "SP_Product_SelectAll";
        SqlDataReader reader = command.ExecuteReader();
        DataTable table = new DataTable();
        table.Load(reader);
        return View(table);
    }
```

## Step 3: Create a View to Display the Product List

```html
    @using System.Data;

    @{
        ViewData["Title"] = "Product List";
    }

    <h1>Product List</h1>
    <a asp-action="AddNewProduct" asp-controller="Product" class="btn btn-success me-3">Add Product</a>
    <a asp-action="ExportToExcel" asp-controller="Product" class="btn btn-primary">Export Excel</a>
    <table class="table">
        <thead>
            <tr>
                <th scope="col">ProductID</th>
                <th scope="col">Product Name</th>
                <th scope="col">Product Price</th>
                <th scope="col">Product Code</th>
                <th scope="col">Product Description</th>
                <th scope="col">UserID</th>
                <th scope="col">Action</th>
            </tr>
        </thead>
        <tbody>
            @foreach (DataRow product in Model.Rows)
            {
                <tr>
                    <th scope="row">@product["ProductID"]</th>
                    <td>@product["ProductName"]</td>
                    <td>@product["ProductPrice"]</td>
                    <td>@product["ProductCode"]</td>
                    <td>@product["Description"]</td>
                    <td>@product["UserID"]</td>
                    <td>

                        @* <a class="btn btn-danger" asp-controller="Product" asp-action="Delete" asp-route-ProductID="@product["ProductID"]">Delete</a> *@
                        @* <form method="post" asp-controller="Product" asp-action="Delete">
                            <input type="hidden" name="ProductID" value="@product["ProductID"]" />
                            <button type="submit" class="btn btn-outline-danger btn-xs">
                                <i class="bi bi-x">Update</i>
                            </button>
                        </form> *@


                        <form method="post" asp-controller="Product" asp-action="Delete">
                            <input type="hidden" name="ProductID" value="@product["ProductID"]"/>
                            <button type="submit" class="btn btn-danger btn-xs">
                                <i class="bi bi-x">Delete</i>
                            </button>
                        </form>
                        <a class="btn btn-primary mt-2" asp-controller="Product" asp-action="EditUser" asp-route-ProductID="@product["ProductID"]">Update</a>
                    </td>
                </tr>
            }
        </tbody>
    </table>
```

## Step 4: Create an Action Method to Export Data to Excel

```csharp
    // Import at the top of the file
    using OfficeOpenXml;
    using LicenseContext = OfficeOpenXml.LicenseContext;


    // Method to Export the data of the list to the Excel
    public IActionResult ExportToExcel()
    {
        ExcelPackage.LicenseContext = LicenseContext.NonCommercial; // Set the license context

        List<ProductModel> productsList = new List<ProductModel>();
        string connectionString = this.configuration.GetConnectionString("ConnectionString");
        SqlConnection connection1 = new SqlConnection(connectionString);
        connection1.Open();
        SqlCommand command1 = connection1.CreateCommand();
        command1.CommandType = CommandType.StoredProcedure;
        command1.CommandText = "SP_Product_SelectAll";
        SqlDataReader reader = command1.ExecuteReader();
        while (reader.Read())
        {
            ProductModel productModel = new ProductModel();
            productModel.ProductID = Convert.ToInt32(reader["ProductID"]);
            productModel.ProductName = reader["ProductName"].ToString();
            productModel.ProductPrice = Convert.ToUInt32(reader["ProductPrice"]);
            productModel.ProductCode = reader["ProductCode"].ToString();
            productModel.Description = reader["Description"].ToString();
            productModel.UserID = Convert.ToInt32(reader["UserID"]);
            productsList.Add(productModel);
        }
        connection1.Close();

        using(ExcelPackage package = new ExcelPackage())
        {
            var worksheet = package.Workbook.Worksheets.Add("Products");
            worksheet.Cells[1, 1].Value = "ProductID";
            worksheet.Cells[1, 2].Value = "ProductName";
            worksheet.Cells[1, 3].Value = "ProductPrice";
            worksheet.Cells[1, 4].Value = "ProductCode";
            worksheet.Cells[1, 5].Value = "Description";
            worksheet.Cells[1, 6].Value = "UserID";

            int row = 2;
            foreach(var product in productsList)
            {
                worksheet.Cells[row, 1].Value = product.ProductID;
                worksheet.Cells[row, 2].Value = product.ProductName;
                worksheet.Cells[row, 3].Value = product.ProductPrice;
                worksheet.Cells[row, 4].Value = product.ProductCode;
                worksheet.Cells[row, 5].Value = product.Description;
                worksheet.Cells[row, 6].Value = product.UserID;
                row++;
            }

            var stream = new MemoryStream();
            package.SaveAs(stream);
            stream.Position = 0;
            string excelName = $"ProductList--{DateTime.Now:dd/MM/yyyy}.xlsx";

            return File(stream, "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", excelName);
        }
    }
```

## Step 5: Run the Application

Run the application and navigate to the `ProductList` action method. Click on the `Export Excel` button to export the data to an Excel file.
