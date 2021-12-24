---
title: Find / Filter 
---

I have to admit. I was gaining a bit of an ego before I was confronted with the find and filter function. I just happened to be lucky enough to be breezing through the assignments I was being assigned before this point. Learning how to search for specific elements in a given array is an important method that I'm sure will come up again in the future. 

<h2>Find</h2>

The find function returns the value of the first element in the provieded array that satisfies the testing function. 

```javascript
const fellowship = ['Sam','Frodo','Merry','Pippin'];

function FellowshipFinder(name){
    const findadventurer = fellowship.find((adventurer) => adventurer == name );
    return findadventurer;

}
FellowshipFinder('Pippin')
```
The code above begins with an array, <span style="color:aqua;font-weight:bold">fellowship</span> that holds the names of all our adventurers. We can build a find function to find a specific adventurer in the array by name. 
Inside <span style="color:aqua;font-weight:bold">FellowshipFinder</span> we set a variable <span style="color:aqua;font-weight:bold">FellowshipFinder</span> equal to the <span style="color:aqua;font-weight:bold">.find</span> function working specifically on our <span style="color:aqua;font-weight:bold">fellowship</span> array. <br>

<span style="color:aqua;font-weight:bold">.find</span> accepts two agruments. a callback function and an optional value to use for <span style="color:aqua;font-weight:bold">this</span> inside the <span style="color:aqua;font-weight:bold">.callback</span> function. The callback function will execute on each element of the array and can accept the following three arguments: <span style="color:aqua;font-weight:bold">element</span>(current element), <span style="color:aqua;font-weight:bold">index</span>(the index of the current element), <span style="color:aqua;font-weight:bold">array</span>(that the find was called on). For our purposes we just had to compare the current element with the name that given to us as an input. The function checks to see if the element in the array matches the name we specified and stores it in <span style="color:aqua;font-weight:bold">findadventurer</span>, allowing us to return the name of our hobbit from within the variable. 

<h2>Filter</h2>

```javascript
const characters = [
             { name: 'Aragorn', race: 'Human' },
             { name: 'Boromir', race: 'Human'},
             { name: 'Gimli', race: 'Dwarf' },
             { name: 'Legolas', race: 'Elf' },
             { name: 'Frodo', race: 'Hobbit' },
             { name: 'Sam', race: 'Hobbit' },
             { name: 'Pippin', race: 'Hobbit' },
             { name: 'Merry', race: 'Hobbit' },
             { name: 'Gandalf', race: 'Wizard' },
             { name: 'Treebeard', race: 'Ent' },
];

function FellowshipFilter(race){
   const adventurer = characters.filter(character => character.race === race);
    return adventurer;
}

FellowshipFilter('Hobbit')

```
The <span style="color:aqua;font-weight:bold">filter</span> method allows us to create an array containing only elements that pass a certain test. It takes a <span style="color:aqua;font-weight:bold">callback</span> parameter and returns an array containing all values that the callback returns as <span style="color:aqua;font-weight:bold">true</span>. Notice how the <span style="color:aqua;font-weight:bold">callback function</span> inside the filter method checks the key value <span style="color:aqua;font-weight:bold">race</span> for each element in the object. The function then returns an array of elements associated with the specified race, in this case it would return all the Hobbits in our party. 

<p><img src="{{site.url }}{{site.baseurl}}/src/assets/img/postpix/fellowship.png" alt="fellowship" /></p>
