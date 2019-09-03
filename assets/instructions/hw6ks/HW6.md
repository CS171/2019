---
layout: lab
exclude: true
---

<img src="cs171-logo.png" width="200">

&nbsp;

# Homework 6

This homework requires that you have read and **programmed** along with:

- [http://www.cs171.org/2019/assets/material/cs171-lab6-reading.pdf](http://www.cs171.org/2019/assets/material/cs171-lab6-reading.pdf)
- The four chapters about JS objects at [http://www.w3schools.com/js/js_object_definition.asp](http://www.w3schools.com/js/js_object_definition.asp) (repetition of JS objects with more examples)
- Optional reading: [Scope and *this* in JavaScript](https://software.intel.com/en-us/xdk/blog/javascript-this-that-and-the-other-thing)



## 1) A survey about the diffusion of microfinance loans (8 points)

In this homework you will work with survey data on microfinance loans. Banerjee et al [1] collected the data in order to develop a model of information diffusion in social networks. To come right to the point: you don't need any knowledge or additional information on microfinance. We will only cover a very small part of the collected data to demonstrate you how to visualize survey results efficiently with JS and D3.

The authors of the study collected detailed demographic and network data in 75 villages in India, six months before a microfinance institution began offering their products to these villagers. The combination of the original survey, information about first-informed villagers and results of the subsequent participation made it possible to draw conclusions on the diffusion of information.

If you are interested in the study you can get the paper here:
[http://science.sciencemag.org/content/341/6144/1236498](http://science.sciencemag.org/content/341/6144/1236498)

[1] Banerjee, Abhijit, et al. "The diffusion of microfinance." *Science* (2013).

### Data

- **Household characteristics** has demographic information about a household's home (own or rent, electricity, number of rooms, latrine type, etc.)
- Individual characteristics with detailed demographic information (age, language skills, occupation, etc.) were collected for some households
- Extensive data about relationships between these households

*In this assignment you will only work with the household characteristics dataset. You will have to use the following columns:*
	 
- ```survey``` ... this variable was generated by us. Imagine it is the date when the household was surveyed.
- ```ownrent``` ... if the home is owned, rented or given by government
- ```hohreligion``` ... religion they belong
- ```electricity``` ... whether the household has private/government/no electricity 
- ```latrine``` ... type of latrine (own/common/no) in the household


### Template

To help you get started with this homework assignment we have prepared a template that you can use. It is based on the front-end framework *Bootstrap* and it includes the JS libraries: *D3* and *jQuery*. Furthermore, a CSV file (```household_charateristics.csv```) is stored in the folder *"data"*. Of course, you can also start with an empty project and just copy the dataset.

[http://www.cs171.org/2019/assets/scripts/hw6/template.zip](http://www.cs171.org/2019/assets/scripts/hw6/template.zip)


### Overview

In the last lab you have learned how to link multiple views with each other, how to make visualization components reusable and how to use D3's brush component to filter the data.

You should now apply what you have learned to create an interactive visualization with multiple views. Your final webpage should contain an area chart and several barcharts. Whenever the user changes the selection - via the brushing component in the area chart - all the bar charts should be updated accordingly.

*You can use the following mockup as a guide:*

![Homework 6 Preview](cs171-hw6-preview.gif?raw=true "Homework 6 Preview")


**Implementation checklist:**

- Create a 2-column layout in HTML & CSS
- Implement visualizations (area and bar charts) as reusable components
- Use D3's brushing component to engage users and help them to filter data efficiently
- Link all the bar charts to the selected region of the brushing component
- Use transitions to make changes smoother
- Make your webpage/visualization responsive for different screen sizes 


### Implementation

1. **Download the resources**

	Please download the template and the CSV file as a ZIP file: [http://www.cs171.org/2019/assets/scripts/hw6/template.zip](http://www.cs171.org/2019/assets/scripts/hw6/template.zip)

2. **Familiarize yourself with the provided framework and the dataset**
	 
		
3. **Specifiy the webpage layout**

	You should create a 2-column layout in HTML and CSS. The area chart with the time period filter should be in the left column and the bar charts should be in the right column. We have included *Bootstrap* in the template so that you can complete this task quickly.

		
4. **Load the CSV data and create the bar chart objects**

	We have already prepared a few code snippets to help you get started. Open the files ```main.js``` and ```barchart.js``` and get an overview.
	
	Similar to our last lab you have to implement visualizations as reusable components. This is especially important if you want to use the same visualization type (e.g., bar charts) several times on the same webpage with different data in each visualization (e.g., bar chart).

	The goal is to implement a generic bar chart object in ```barchart.js``` that can be used in ```main.js``` multiple times for these variables:
	
	-  ```ownrent```
	-  ```electricity```
	-  ```latrine```
	-  ```hohreligion```

	Example: You can find the values *Christianity*, *Hinduism* and *Islam* in the column ```hohreligion```. One of your bar charts should show how many households belong to which religion.
	
	![Homework 6 Barchart](cs171-hw6-barchart.png?raw=true "Homework 6 Barchart")
	
	Each bar chart should contain a title, a y-axis and labels for each bar.
	
	&nbsp;
	
	------
	
	#### Grouping data with d3.nest
	
	D3 includes a powerful functionality to transform a flat data structure into a nested one. With ```d3.nest()``` you can group data by a categorical variable. If you work with temporal data you can combine all values for each day. We would highly recommend you to use this function to prepare the data for your bar charts.
	
	**Example**
	
	*CSV-Data:*
	
	```
	student,city
	John,Boston
	Sarah,Boston
	Stacy,New York
	Anna,London
	Mike,Boston
	```
	
	First, let's use *d3.nest()* to group the rows by *city*:
	
	```javascript
	var studentsByCity = d3.nest()
  		.key(function(d) { return d.city; })
  		.entries(students);
	```
	
	As a result we get a nested structure:
	
	```javascript
	studentsByCity = [
		{ key: "Boston", values: [
			{student: "John", city: "Boston" },
			{student: "Sarah", city: "Boston" },
			{student: "Mike", city: "Boston" }
		]},
		{ key: "New York", values: [{student: "Stacy", city: "New York"}] },
		{ key: "London", values: [{student: "Anna", city: "London"}] }
	];
	```
	
	We can further extend the d3.nest() function with ```rollup``` to aggregate the values for each group (e.g. sum, count, avg):
	
	```javascript
	var countStudentsByCity = d3.nest()
  		.key(function(d) { return d.city; })
  		.rollup(function(leaves) { return leaves.length; })
  		.entries(students);
	```
	
	This example gives us the number of students for each city:
	
	```javascript
	countStudentsByCity = [
		{ key: "Boston", values: 3 },
		{ key: "New York", values: 1 },
		{ key: "London", values: 1 }
	];
	```
	
	You can read more about D3's nest functionalities and see some examples here:
	
	- [http://bl.ocks.org/phoebebright/raw/3176159/](http://bl.ocks.org/phoebebright/raw/3176159/)
	- [https://github.com/d3/d3-collection/blob/master/README.md](https://github.com/d3/d3-collection/blob/master/README.md)
	
	
	------
	

	&nbsp;


5. **Implement the area chart**

	The area chart should display the number of surveys that were conducted over time. Furthermore, it should enable the user to specify a desired time range which automatically influences the bar charts in the right column.

	1. Create a new area chart object
	2. Define the SVG drawing area and the axes
	3. Prepare the data in the ```wrangleData()``` function. You can use d3.nest() again to count the number of surveys for each day. Make sure that you have the date variable stored as a date object and that the days are in correct order.
	4. Initialize D3's path generator for the area (```d3.area()```)
	5. Call the area function and draw the actual paths


6. **Use a brushing component in your area chart as a time period filter**
	
	The idea of brushing is to allow the user to select a subset of data interactively. Changes are automatically reflected in the linked visualizations, which are the bar charts in our case.
	
	Initialize the brushing component (```d3.brushX()```) and append it to the drawing area. Whenever the user changes the selection, call the ```brushed()``` function in ```main.js```.

7. **Implement the ```brushed()``` function and connect it with the bar charts**

	Get the range of the selected region (start and end date) in the area chart and call the ```selectionChanged()``` function in all the linked bar charts.

8. **Use transitions to make changes smoother**

	Integrate D3's transitions into your bar charts (rectangles, labels).


9. **Make your webpage and your visualizations responsive**

	Until now you have probably specified a fixed size for your SVG drawing areas every time. This is more convenient but actually far from ideal if you want to address users with different screen resolutions, which is our goal in most scenarios. 
	
	There are different ways of implementing more flexible visualizations. In our case, Bootstrap - a responsive, mobile-friendly front-end framework - makes it already very easy to create a responsive webpage, just by using the *fluid grid system* ([http://getbootstrap.com/css/#grid](http://getbootstrap.com/css/#grid)). You can use the grid classes to set up the page layout and to define at which point columns should wrap onto a new line.
	
	 In addition you can create your own CSS media queries to make individual adjustments (e.g. font size, spacings, ...) for different screen sizes. You can read more about it here: [https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)
 
 You should now be able to display the area and bar charts below each other if the screen is not wide enough. But this doesn't solve the problem of the fixed-sized SVG areas, so you need one further extension. Whenever you initialize a new visualization you can check the width of the parent container and set the SVG width accordingly. The height can remain unchanged.
 
 *Example for getting the width of a div element with jQuery:*
 
 ```javascript
 $("#chart-area").width()
 ```
 
 Try this concept in your current webpage and make the visualizations more flexible!
 

10. **Create a proper style for your webpage**

	Maintain good spacing between UI components, overall layout, font size, color scheme, etc.
	
## 2) Design (2 points)
Look at the visualizations philosophers by Marian Doerk at [http://mariandoerk.de/edgemaps/demo/](http://mariandoerk.de/edgemaps/demo/). 

Look at and interact with the visualization and critique its design by answering the following questions:

	- Who is the intended audience?
	- What questions does this visualization answer? 
	Detailed questions:
	- What data types are represented in this visualization? Is the data qualitative, quantitative, or ordered?
	- How is the data visually encoded - what are the marks and channels? 
	- How is color being used, what type of color scale do you see? 
	- How could you improve the efficiency of the visualization by changing marks and channels and/or the used colors.
	

## 3) Bonus Task (1 point)

Please make sure to finish al previous tasks before you start with the bonus activity. Extra credit is only given if the rest of the homework has been completed and the full possible points have been received. This task is intended for those of you who have more experience with web development and JS.

You have implemented multiple visualization components and made them responsive to different screen sizes so far. If you have followed our concept you may have noticed that the layout will break if you resize your browser window.

Think about an efficient way how to handle this and implement it in your current webpage. 



## 4) Submit Homework in Canvas

Submission instructions:

1. Use the following recommended folder structure:

```
/submission_FirstnameLastname	
	hw/
	    implementation/ ...folder for your code
   	        index.html
	        css/ 		...folder with all CSS files
	        js/ 		...folder with all JavaScript files
	    design/         ...folder for your sketches

	    ...
```

2. Make sure to keep the overall size of your submission under 5MB! Sketches don't have to be in the highest resolution, but should still be readable.

3. Upload a single .zip file.

4. Please submit your lab _separately_ on canvas.

**Congratulations for finishing Homework 6! See you in class!**