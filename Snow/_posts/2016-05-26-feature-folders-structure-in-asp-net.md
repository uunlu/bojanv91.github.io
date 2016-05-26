---
layout: post
title: "Feature Folders" structure in ASP.NET MVC
---

DRAFT

Structuring things around **business concerns** is more convenient and natural than structuring them around **technical concerns**. In both ways, [Separation of Concerns](http://deviq.com/separation-of-concerns/) is applied, but from which point of view a better structure is achieved at this level?<!--excerpt-->

How changing requirements usually come from project managers?

- Change these fields in all these views; all these models; all these controllers, or
- Change these fields in customer registration; shopping cart payment; ...

Most of the time developers make modifications scoped to a single feature (e.g. adding new field). Structuring the related files under single folder, can make this simpler. By applying the rule *"Files that change together should be structured close together"*, we can easily see the default MVC folder structure violates it. That is why structuring by business concerns is extremelly important aspect.

Let's see both examples. 

# Structure by technical concerns (horizontal) - the default ASP.NET MVC way

        Controllers
            CustomersController.cs
            OrdersController.cs
            ShoppingCartController.cs
            ProductsController.cs
            ...
        Models
            CustomersIndexModel.cs
            CustomersEditModel.cs
            CustomersRegisterModel.cs
            CustomersLoginModel.cs
            OrdersIndexModel.cs
            OrdersDetailsModel.cs
            ShoppingCartCustomerDetailsModel.cs
            ShoppingCartPaymentDetailsModel.cs
            ...
        Views
            Customers
                Index.cshtml
                Edit.cshtml
                Register.cshtml
                Login.cshtml
            Orders
                Index.cshtml
                Details.cshtml
            ShoppingCart
                _MiniCart.cshtml
                Index.cshtml
                Orders.cshtml
                PaymentDetails.cshtml
                CustomerDetails.cshtml
            Products
                ...


# Structure by business concerns (vertical) - alternative (and better) way

        Features
            Customers
                CustomersController.cs
                Index.cshtml
                Index.cs
                Edit.cshtml
                Edit.cs
                Register.cshtml
                Register.cshtml
                Login.cshtml
                Login.cshtml                
            Orders
                OrdersController.cs
                Index.cshtml
                Index.cs
                Details.cshtml
                Details.cs
            ShoppingCart
                _MiniCart.cshtml
                ShoppingCartController.cs
                Index.cshtml
                Index.cs
                CustomerDetails.cshtml
                CustomerDetails.cs
                PaymentDetails.cshtml
                PaymentDetails.cs
                Orders.cshtml
                Orders.cs
            Products
                ...

Food for thought:
 
- What if we put our JavaScript files also in these feature folders?
- What if one feature folder becomes so demanding on the UI that needs to be a full SPA view/module - can we structure only that feature to use Angular(or React, or whatever)?

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

Structuring files by business concerns makes things easier to find and developers don't step over each other toes spending time fixing merge conflicts. Also, each feature can scale on it's own, independent from other features. 

At our company we use this style of structure on over dozen projects for more than a year, and due the success and productivity boost that it gave to our teams - now this is our default structure on the presentation layer. But, not only there! In following posts I'll give more insights. Stay tuned.

Happy coding!  