---
layout: page
title: Something smells bad in the DNIe sample Android app (Part 1)
teaser: A sample Android app has been published by the Spanish Homeland Ministry and the National Police to explain how to interact with the new version of the DNIe 3.0 (the Spanish ID card). However, the published code is not what we can consider "best practices" in software development.
categories:
    - development
tags:
    - app clinic
    - bad design
    - code smells
    - UI / UX
breadcrumb: true
header:
    image_fullwidth: "header_development.png"
---

In the past years, I have been in contact to many people who have tried Android development. I guess it has become mainstream in many Universities as mobile development is very attractive and has real good business opportunities. However, I have experienced that most people can create (toy) Android apps, but not so many are able to build good software for mobile devices. Moreover, I still miss a proper course on this subject in the syllabus of many Bachelor Programs.

Some days ago, I found that the Spanish Homeland Ministry and the National Police had released some material for developers in order to interact with the new DNIe 3.0 (the Spanish identification card), based on **Near Field Communication** (NFC), together with the code of an Android sample app. When I looked at the code, I found good evidence of what I stated before: not everybody is able to build good software for mobile devices.

Thus, I have decided to dissect the code in order to point out the things that I consider that should be improved, specially considering that this should be a reference app for many developers. I have divided it into two parts because it became too long for a single post. By the way, the code can be downloaded from [here](http://www.dnielectronico.es/PortalDNIe/PRF1_Cons02.action?pag=REF_1100) by clicking on the last item saying *Ejemplo Aplicaciones DNIe* (it's in Spanish). 

## Tools

### IDE usage

According to what we can see at plain sight when the downloaded file is uncompressed, the employed IDE seems to be Eclipse, even though most of the community moved to Android Studio long ago. This might be considered a personal choice of the developers, although it is highly advisable to use Android Studio in order to have the latest improvements Google is incorporating into this IDE.

### Lack of Gradle usage

Since most of the Android community is using Android Studio, Gradle is the main choice in order to manage dependencies in the project. However, this sample app lacks such dependency manager. Instead, several `.jar` files are included into the `libs` folder, making difficult to update these dependencies when new versions are available. By the way, there is also a `lib` folder (without an *s*) which happens to be empty.

### Version Control and Release

GitHub is a *de facto* standard in the software development community for open sourcing projects. This would have been a more appropriate option to release this sample app code. The community could have found issues in the development and contribute by fixing them or adding new features. However, only a `.rar` file is provided and there is no apparent way of communicating bugs or improvements.

## Software engineering

### Architecture

Once the project is opened, first thing I tried to do was to check out the package structure in order to have a clear understanding of the project architecture. Even though the sample code is accompanied of several `.pdf` files talking about architecture, there is no such thing in the code that reflects the architecture of the app. 

Most of the classes are located in the base package called `com.dniemcu`, except for one class which is located in the package `com.dniemcu.gui`. It also happens that there are several other classes, such as `pdfload.java` which also deal with UI related stuff, so this packaging seems to be accidental.

### Tests

There are no tests for this app. I could have understood the absence of UI automated tests because I consider using **Espresso** a little of a pain, but how are you supposed to know if this thing actually works? Nevertheless, with the current state of the project, it will be painful to try to add any test at all, although they will be needed to try to refactor this code to improve it.

### Object-Oriented Design

With no architecture and no tests in this project, I don't expect a good object-oriented design either, but there are some points that are worth mentioning.

There are two classes, `Compress.java` and `CompressFolder.java`, which semantically are very similar. Their purpose is to zip a file or a folder, respectively. However, the way in which they are implemented is totally different. `Compress.java` is supposed to be used as an instance. On the other hand, `CompressFolder.java` has only static methods. There is no consistency between them, which will turn in a cognitive overload for the developer who makes use of them. Besides, static methods are an obstacle for testing and usually reflect a bad object-oriented design.

In addition, something even worse dragged my attention in `dnieutils.java`. As you can expect, this class is a utility class which doesn't have any instance variables. The surprise is in the following method:

```java
public class dnieutils{
	public String parseXML (String tag, String xmldoc){
		String strModulus = null;
		
		dnieutils auxFunc = new dnieutils();
		...
		strModulus = auxFunc.convertStringToHex(((Node)textFNList.item(0)).getNodeValue().trim());
	}
}
```

That is, an instance of the same class (remember, without fields) is created to invoke an instance method, which could have been anyway invoked without such instance. This reveals a poor understanding of object-orientation and the Java language.

Apparently, looking at the methods in `dnieutils.java`, it seems that there are many things in this class that will be needed across different projects. My question then is: why isn't this included as part of the provided library? Are we going to duplicate all this code in every single project?


### Violations of the SOLID Principles

Since there is no separation of concerns, this leads to monolithic pieces of code where all the application logic is inside large activities mixing up visualization code with, for instance, persistence code, and thus, violating the Single Responsibility Principle.

Also, dependencies are not decoupled behind interfaces and exposed in the constructor of the class, therefore violating the Dependency Inversion Principle.

## Code

At this point, you can imagine this is not going to be precisely what we understand as [Clean Code](http://www.amazon.es/Clean-code-Handbook-Software-Craftsmanship/dp/0132350882) (which I recommend to be your bible for coding), but there are several things worth mentioning.

### Lack of coding standards

Although there is much left to personal preference in this regard, usually it is advisable that code looks like it has been written by a single person even though there might be a team behind it. Given the size of this project, I seriously doubt there was more than one person working on it, but it looks like it was written by different people.

For instance, checking braces at `if` and `for` statements, sometimes they are inline, sometimes they are on the next line by themselves, and some other times they are missing when there is only a single statement after the `if`. It mainly reveals that much of the code has been copyed and pasted from different sources and never reviewed to make it look nice in the project.

### Bad indenting

I thought this problem was solved with modern IDEs since they take care of indenting correctly automatically, but I guess I underestimate how badly someone can mess with indenting code files. Lucky they were not programming in Python.

### Commented-out Code

Across many different files, we can find commented-out code. If it is not supposed to be there, it should have been deleted. If you the developer had used a version control system in case of being afraid to delete something that may be necessary later, this wouldn't have happened.

### Bad Naming

This is probably one of the worst aspects of this app. Several examples of bad naming are:

* Class names using lowercase: `dnieutils.java`, `pdfload.java`, `pdfsigned.java`...
* Methods starting with an uppercase letter: `CalculaBitParidad` in `dnieutils.java`.
* Inconsistent naming mixing English and Spanish.
* Inconsistent instance variable naming, using old-school prefixes: `m_success`, `m_bRestart` in `PrKOperations.java`; `m_url` in `DNIeMcu.java`...
* Indescriptive naming: `DNIeMcu`, `MyAppMcu`... It seems `Mcu` is something relevant, but I have no clue about what it means.
* Idioms: usually in Android, classes extending `Activity` (or some subclass of it for compatibility) are suffixed with such name (e.g. `pdfload.java` is an activity and therefore should have been named `PDFLoadActivity.java`). No activity subclass in this project applies such idiom. The same thing applies when extending `Fragment`. However, we can find the class `MyPasswordFragment`, but it turns out that it doesn't extend `Fragment`.
* Comment abuse: several methods have inlined comments explaining each step, which reveals a bad naming. For instance (comments in Spanish):

``` java
RelativeLayout vwParentRow = (RelativeLayout)v.getParent();				    	
int position = listView.getPositionForView(vwParentRow);
	    			
// Dejamos indicado el fichero sobre el que vamos a trabajar
for(int idx=0;idx<pdfItems.size();idx++)
	pdfItems.get(idx).selectPDF(false);
	        		
// Marcamos el PDF actual
pdfItems.get(position).selectPDF(true);
	        			        		
// Habilitamos el botón de CONTINUAR
Button btnCont = (Button)findViewById(R.id.Btn_CONT);
if(!btnCont.isEnabled())
{
	btnCont.setEnabled(true);
	btnCont.setAlpha(1);
}
	        		
// Forzamos el repintado
mAdapter.notifyDataSetChanged();

// Calculamos la ruta al fichero
String fileName= inboxDir.getPath()+"/"+pdfItems.get(position).getFileName();
```

That goes on, but I don't want to paste the whole thing here; I guess you can see the point.

### Code duplication

Take a look at `pdfload.java`, `pdfalreadysend.java` and `pdfsigned.java`. They all have an inner class called `PDFItem` and another one called `CustomArrayAdapter` (which by the way, isn't very descriptive). The code inside them is slightly different, but most of it does exactly the same rendering. That is something that we could have imagined looking at the class names, which seems to list a bunch of `.pdf` files at different stages in the process of signing. This place is screaming for a refactoring to extract those classes out and perhaps make subclasses for the small variations in each case.

### Reinventing the wheel

One of the most amazing things found in `dnieutils.java` is its last method:

``` java
public long min(long num1, long num2) {
	// TODO Auto-generated method stub
	if(num1>num2)
		return num2;
	else
		return num1;
}
```

Not only reinventing the wheel, but also leaving the ugly `TODO` comment auto-generated by Eclipse.

### Long lists of arguments

Consider the following method signature, found on `PDFSignature.java`:

``` java
public PdfSignatureAppearance sign(Image image, 
		String src, 
		String tmp, 
		String dest,
    	Certificate[] chain, 
    	PrivateKey privKey, 
    	String digestAlgorithm,
    	String provider,
    	CryptoStandard subfilter,
    	String reason, 
    	String location) throws 
    		GeneralSecurityException, 
    		IOException, 
    		DocumentException 
```

11 arguments and 3 very different `Exception` thrown. Clearly, this shows either a violation of the **Single Responsibility Principle** or a bad design of the domain. For instance, I can guess a relationship between several arguments which could be together into the same object.

### Long methods

There are several classes with methods longer than 100 lines of code. This clearly deserves some refactoring.

### Abuse of inner an anonymous classes 

There are files with up to 4 inner classes, which obscure the project structure just looking at file names. Also, I have counted up to 4 anonymous nested classes in `DirectoryChooserDialog.java` (which, guess what, doesn't extend any type of Android Dialog).

### Bad management of exceptions

There are `try-catch` blocks with more than 100 lines of code in the `try` part and then you find a `catch` clause like this:

``` java
catch (Exception e) 
{
	// TODO Auto-generated catch block
	e.printStackTrace();
}
```
This reminded me of the following tweet.

<blockquote class="twitter-tweet" lang="es"><p lang="en" dir="ltr">There&#39;s a lot wrong with this method, so I&#39;ll just wrap all 1400 lines with a try/catch. That should handle anything that comes up.</p>&mdash; Code Standards (@codestandards) <a href="https://twitter.com/codestandards/status/612403546227486720">junio 20, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

### Obfuscated algorithms

I challenge any of you to guess what this method is doing (I was not able):

``` java
public long getTLV( int [] iTagLen, byte [] tlv){
	Long	tag;
	int	idxByte = 0;
	
	tag = (long) tlv[idxByte++];
	if( (tag & 0x1f) == 0x1f )
		tag = (tag<<8) | tlv[idxByte++];
		
	if( (tlv[idxByte] != (byte)0x81 ) && (tlv[idxByte] != (byte)0x82 ) ){
		iTagLen[0] = idxByte + 1;
		return tlv[idxByte];
	}else if( tlv[idxByte] == (byte)0x81 ){
		iTagLen[0] = idxByte + 2;
		return (tlv[idxByte+1])& 0x00ff;
	}else if( tlv[idxByte] == (byte)0x82 ){
		iTagLen[0] = idxByte + 3;
		return (0x100 + tlv[idxByte+=2])& 0xffff;
	}else
		return 0;
}
```

Magic numbers, bitwise operations, indescriptive naming, random braces, return types that do not match what they are returning, modification of input arguments... Too many bad things in just less than 20 lines of code. I am still wondering why `tag` is computed and never used.

---

I think I already made my point with plenty of evidence, but there are still some Android-specific things that I would like to cover. Since this post is long enough, I'll leave them for the second part. Meanwhile, feel free to discuss and comment below!


 [1]: #
 [2]: #
 [3]: #
 [4]: #
 [5]: #
 [6]: #
 [7]: #
 [8]: #
 [9]: #
 [10]: #
 