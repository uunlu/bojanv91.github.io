---
layout: post
title: Towards good enough code: Re-factoring a business rule check with the Specification Pattern
---

The other day, one of my colleges asked me for a code review on a specific part of code that was written and I said let's dig a little deeper into the options that we have. In this article I demonstrate the re-factoring steps in detail that we've taken and eventually employed the `Specification Pattern` <!--excerpt-->. Have in mind that, I choose a very basic example in order to keep things simple and avoid confusion that can be arouse from domain complexity.

Here is the original code:  
  	
	//..

	var newCompany = new Company(message.Name, message.CountryId);
	
	//query all companies from database 
	var companies = _companyRepository.Query().ToList();
	//check if the newly created company is unique
	if (companies.Any(x => x.Name == newCompany.Name && x.CountryId == newCompany.CountryId))
		throw new Exception("A company with the same name and country already exists");

	session.Save(newCompany);
	//..

Here we can see few problems. First all companies are queried from database, that can create performance issues. Another problem is too much operations happening in the `If` check; thus, making the code harder to read. And the final problem is very plain practice of `Exception` throwing that can be better, although, I like expressing explicit guard check. Let's tackle these problems one by one in few steps along this article and provide suggested improvements.

Also, I provide here the `tl;dr;` version of the code:

	//..

	var newCompany = new Company(message.Name, message.CountryId);
	
	var spec = new UniqueCompanySpecification(_companyRepository);
	if (spec.IsSatisfiedBy(newCompany) == false)
		throw new CompanyAlreadyExistsException();

	session.Save(newCompany);
	//..

# How we get there?

## Step 1 - solve the query performance issues

	var numberOfSameCompanies = _companyRepository.Query()
		.Where(x => x.Name == newCompany.Name && x.CountryId == newCompany.CountryId)
		.Count();
	if (numberOfSameCompanies > 0)
		throw new Exception("A company with the same name and country already exists");

The above query retrieves the number of companies satisfying the given `where` condition. Performance issues solved.

## Step 2 - make the `if` condition check explicit 
	
	var numberOfSameCompanies = _companyRepository.Query()
		.Where(x => x.Name == newCompany.Name && x.CountryId == newCompany.CountryId)
		.Count();
	var doesCompanyAlreadyExists = numberOfSameCompanies > 0;
	if (doesCompanyAlreadyExists)
		throw new Exception("A company with the same name and country already exists");

By making some conditions explicit, we gain clear understanding about what the code does.

## Step 3 - make the business rule violation explicit 

Original:

	throw new Exception("A company with the same name and country already exists");

Re-factored to:

	throw new CompanyAlreadyExistsException();

And the implementation of the exception:

	public class CompanyAlreadyExistsException : Exception
	{
	    CompanyAlreadyExistsException () 
	      :base("A company with the same name and country already exists")
	    { 
		}
	}

Now, it looks better. Anyway, we have still room for improvements.

## Step 4 - encapsulate the business rule check by employing the Specification Pattern

Specification is a tactical design pattern presented in Eric Evans’ book Domain Driven Design. The `Specification Pattern` is a way of encapsulating business rule(s) and test it against a candidate object to see if that object satisfies all the requirements expressed in the specification. This pattern fits very good with the Single-Responsibility-Principle (SRP), which states that one class should have only one reason to change. Furthermore, this specification object can be easily unit tested and reused.  
  
Here you can see how it is used:

	var spec = new UniqueCompanySpecification(_companyRepository);
	if (spec.IsSatisfiedBy(newCompany) == false)
		throw new CompanyAlreadyExistsException();

And the implementation details:
	
	public class UniqueCompanySpecification : ISpecification<Company>
	{
		readonly ISession _companyRepository;

		public UniqueCompanySpecification(CompanyRepository companyRepository)
		{
			_companyRepository = companyRepository;
		}

		public bool IsSatisfiedBy(Company candidate)
		{
			var numberOfSameCompanies = _companyRepository.Query()
				.Where(x => x.Name == newCompany.Name && x.CountryId == newCompany.CountryId)
				.Count();
			bool isUnique = numberOfSameCompanies == 0;
			return isUnique;
		}
	}

	public interface ISpecification<T>
	{
		bool IsSatisfiedBy(T candidate);
	} 

After all re-factoring steps the final code is as follows:

	//..

	var newCompany = new Company(message.Name, message.CountryId);
	
	var spec = new UniqueCompanySpecification(_companyRepository);
	if (spec.IsSatisfiedBy(newCompany) == false)
		throw new CompanyAlreadyExistsException();

	session.Save(newCompany);
	//..

# Summary

In this article I've shown a re-factoring process and usage of the Specification Pattern in order to satisfy an explicit business rule.     
The re-factoring steps we took:  

1. solve the query performance issues
2. make the `if` condition check explicit
3. make the business rule violation explicit
4. encapsulate the business rule check by employing the Specification Pattern

The Specification Pattern lets you decouple the design of requirements, fulfillment, and validation. Allows you to make your system definitions more clear and declarative, but be careful of the temptations to over-use it.

**References:**

- [Specification Pattern by Eric Evans and Martin Fowler](http://martinfowler.com/apsupp/spec.pdf)
- [https://en.wikipedia.org/wiki/Specification_pattern](https://en.wikipedia.org/wiki/Specification_pattern)
- [Book: Domain Driven Design, Tackling Complexity In The Hearth of Software - by Eric Evans](http://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)