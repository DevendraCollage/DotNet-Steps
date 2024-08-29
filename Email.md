# Email Sender

## Description

Creating a simple email sender in .NET this module is responsible for sending emails to the users. It uses the SMTP (Simple Mail Transfer Protocol) to send emails.

## Functions

### SendMail

This functions sends an email to the user.

#### Parameters

- `Email` : The recipient's email address.
- `Subject` : The subject of the email.
- `Message` : The body of the email.

#### Returns

- `Bool` : True if the email was sent successfully, False otherwise.

## How to do this using C# and .NET Core MVC

### Step 1: Install the `System.Net.Mail.Abstraction` package

- You can add package from the CMD or from the `NuGet` Package Manager of the VisualStudio.

### Step 2: Create the `Email` Controller

#### How to generate App Password for specific App you are using

- Go to your Google Account.
- On the left navigation panel, click Security.
- If you haven’t enabled 2-Step Verification, do so first.
- Search for App Passwords in the search bar at the top, and click on it.
- Name your app, generate the password, and save it securely for later use.

```csharp
    // This is the method used for sending mail
    public IActionResult SendMail(MailModel mailModel)
    {
        try
        {
            MailMessage mail = new MailMessage();
            mail.From = new MailAddress("your email");
            mail.To.Add(mailModel.To);
            mail.Subject = mailModel.Subject;
            mail.Body = mailModel.Body;
            mail.IsBodyHtml = true;
            SmtpClient smtp = new SmtpClient();
            smtp.Host = "smtp.gmail.com";
            smtp.Port = 587;
            smtp.UseDefaultCredentials = false;
            smtp.Credentials = new System.Net.NetworkCredential("your email", "app password");
            smtp.DeliveryMethod = SmtpDeliveryMethod.Network;
            smtp.EnableSsl = true;
            smtp.Send(mail);
            ViewBag.Message = "Email sent successfully!";
        }
        catch (Exception e)
        {
            Console.WriteLine(e.Message);
            ViewBag.Error = "There was an error sending the email: " + e.Message;
            return View("EmailForm");
        }

        return RedirectToAction("EmailForm");
    }
```

### Step 3: Create the `MailModel` class

```csharp
    public class MailModel
    {
        public string To { get; set; }
        public string Subject { get; set; }
        public string Body { get; set; }
    }
```

### Step 4: Create the `EmailForm` View

```html
@{ ViewData["Title"] = "Send Email"; } @model MailModel

<form asp-action="SendMail" method="post">
  <div class="form-group">
    <label for="To">To</label>
    <input asp-for="To" class="form-control" />
  </div>
  <div class="form-group mt-2">
    <label for="Subject">Subject</label>
    <input asp-for="Subject" class="form-control" />
  </div>
  <div class="form-group mt-2">
    <label for="Body">Body</label>
    <textarea asp-for="Body" class="form-control"></textarea>
  </div>
  <button type="submit" class="mt-3 btn btn-primary">Send Email</button>

  @if (ViewBag.Message != null) {
  <div class="alert alert-success mt-3">@ViewBag.Message</div>
  } @if (ViewBag.Error != null) {
  <div class="alert alert-danger mt-3">@ViewBag.Error</div>
  }
</form>
```

### Step 5: Run the Application

### Step 6: Open your browser and navigate to

```bash
    https://localhost:44705/Email/EmailForm
```
