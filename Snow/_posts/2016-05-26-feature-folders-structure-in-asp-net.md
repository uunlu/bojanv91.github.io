---
layout: post
title: "Feature Folders" structure in ASP.NET MVC
---

DRAFT

Structuring things around **business concerns** is more convenient and natural than structuring them around **technical concerns**. In both ways, [Separation of Concerns](http://deviq.com/separation-of-concerns/) is applied, but from which point of view a better structure is achieved at this level?<!--excerpt-->

How changing requirements usually come from project managers?

- Change these fields in all these views; all these models; all these controllers, or
- Change these fields in customer registration; shopping cart payment; ...

Most of the time developers make modifications scoped to a single feature (e.g. adding new field). Structuring the related files under single folder can make this simpler. By applying the rule *"Files that change together should be structured close together"*, we can easily see the default MVC folder structure violates it. That is why structuring by business concerns is extremelly important aspect.

Let's see both examples. 

# Structuring files by technical concerns (horizontal) - the default ASP.NET MVC way

    Controllers
        CustomersController.cs
        OrdersController.cs
        ShoppingCartController.cs
        ProductsController.cs
        ...
    Models
        CustomersEditModel.cs
        CustomersIndexModel.cs
        CustomersLoginModel.cs
        CustomersRegisterModel.cs
        OrdersDetailsModel.cs
        OrdersIndexModel.cs
        ShoppingCartCustomerDetails.cs
        ShoppingCartIndex.cs
        ShoppingCartOrdersList.cs
        ShoppingCartPaymentDetails.cs
        ...
    Views
        Customers
            Edit.cshtml
            Index.cshtml
            Login.cshtml
            Register.cshtml
        Orders
            Details.cshtml
            Index.cshtml
        ShoppingCart
            _MiniCart.cshtml
            CustomerDetails.cshtml
            Index.cshtml
            OrdersList.cshtml
            PaymentDetails.cshtml
        Products
            ...

Now, imagine you have many-many controllers, plus the standard N-Layer stuff like repositories, services, DTOs... things are starting to get messy.

# Structuring files by business concerns (vertical) - alternative (and better) way

    Features
        Customers
            CustomersController.cs
            Edit.cs
            Edit.cshtml
            Index.cs
            Index.cshtml
            Login.cs
            Login.cshtml       
            Register.cs         
            Register.cshtml
        Orders
            Details.cs
            Details.cshtml
            Index.cs
            Index.cshtml
            OrdersController.cs
        ShoppingCart
            _MiniCart.cshtml
            CustomerDetails.cs
            CustomerDetails.cshtml
            Index.cs
            Index.cshtml
            OrdersList.cs
            OrdersList.cshtml
            PaymentDetails.cs
            PaymentDetails.cshtml
            ShoppingCartController.cs
        Products
            ...

In Visual Studio this looks like following:

![Feature Folders example in ASP.NET MVC](/images/2016-05-26-feature-folders-structure-in-asp-net/image01.png)

Food for thought:
 
- What if we put our JavaScript files also in these feature folders?
- What if one feature folder becomes so demanding on the UI that needs to be a full SPA view/module - can we structure it to use Angular (or React, or whatever)?

    ShoppingCart
        Components
            CartComponent.js
            CartComponent.css
            PaymentComponent.js
            PaymentComponent.css
            CartContainer.js
        App.js
        App.css
        Index.cshtml
        ShoppingCartController.cs

# Configuration

To make this work in ASP.NET MVC, the default Razor view engine should be replaced with one that makes the distincion of feature folders.

    public class Global : HttpApplication
    {
        void Application_Start(object sender, EventArgs e)
        {
            // ...
            ViewEngines.Engines.Clear();
            ViewEngines.Engines.Add(new FeatureFoldersRazorViewEngine());
        }
    }
    
    public class FeatureFoldersRazorViewEngine : RazorViewEngine
    {
        public FeatureFoldersRazorViewEngine()
        {
            var featureFolderViewLocationFormats = new[]
            {
                "~/Features/{1}/{0}.cshtml",
                "~/Features/{1}/{0}.vbhtml",
                "~/Features/Shared/{0}.cshtml",
                "~/Features/Shared/{0}.vbhtml",
            };

            ViewLocationFormats = featureFolderViewLocationFormats;
            MasterLocationFormats = featureFolderViewLocationFormats;
            PartialViewLocationFormats = featureFolderViewLocationFormats;
        }
    }

# Summary

Structuring files by features (business concerns) makes things easier to find and manage. 

- You don't step over each other toes with your peers, thus spending time fixing merge conflicts. 
- You can scale and modify each feature on it's own, independent from other features.
- You immediately understand what the application does and where to find the needed files for your given requirement.
- You can easily reuse similar features across projects by simply copying single folder.
- The needed navigation thru Solution Explorer is drastically reduced as everything important for your requirement resides in single folder. 

At our company we use this style of structure on over dozen projects for more than a year, and due the high success and productivity boost that it gave to our teams - this became our default structure on the presentation layer. But, you may ask - how do we structure our application services and data access? Stay tuned...

Happy coding!  