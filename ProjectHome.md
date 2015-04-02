ReinCRUD is a Rapid Application Development (RAD) Framework for the [Vaadin](http://www.vaadin.com) framework under the AGPL 3.0. It is based on Java (of course), Spring and Hibernate. It is currently in **BETA**.

This project is kindly sponsored by [acoveo software development](http://www.acoveo.com).

See the [Download](Download.md) page for instructions how to get reinCRUD. If you would like to use reinCRUD in your commercial projects, you can obtain a license from the [reinCRUD page](http://vaadin.com/directory#addon/reincrud) in the [vaadin addon directory](http://vaadin.com/directory). You can also contact us directly through our website http://www.acoveo.com.

With reinCRUD you can create CRUD applications based on Hibernate entity beans within a few minutes. If you use the provided Vaadin application you don't have to write a single line of code, apart from defining your entities.

The framework is based on annotations, so you can customise the behaviour of the UI components by placing annotations on your entities.

ReinCRUD provides:
  * List
  * Form
  * Search UI

You can use the components separately within your own user interfaces and combine them. The List for example can be filtered by combining it with the Search UI and the Form can be hooked up to the List to edit the entity selected in the list.

ReinCRUD includes all the required maven dependencies for a Spring 3 based Web application with a Hibernate 3 layer for DB access. The only maven dependency you have to add to your project is ReinCRUD! Of course you can still exclude any dependency which is specified by ReinCRUD and substitute a different version.

The easiest way to start a new project is to checkout the reincrudexample project and modify it to suit your needs. Will we provide a more stripped-down example project later on!

You can see a working demo [here](http://www.acoveo.com/reincrudexample/vaadintest) (slow server!) and the explanations on [Reincudexample](Reincudexample.md).

The code is currently hosted on https://www.acoveo.com/svnroot/business/public/trunk/reincrud. You can find the example code on https://www.acoveo.com/svnroot/business/public/trunk/reincrudexample.