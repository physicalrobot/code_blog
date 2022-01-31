---
title: Brainstorm React App
---

This app started off just like any other app, with a simple question. 

<h3 style='text-align: center ;color:aqua'> "What am I going to code?"</h3>
It then occured to me how convenient it would be to have a program that helps me plan out my concepts. The answer felt a bit unimaginative to me at first but considering that it was better than everything else I was coming up with at the time, I decided to lean into it.<br><br>
I now present to you <a href='https://github.com/physicalrobot/brainstorm' style='color: turquoise'>Brainstorm!</a> Your one stop shop when it comes to all your planning needs. The site has three section you can interact 
with. 
<div style='text-align:center;'>
<ul style='display:inline-block ; text-align:left'>
<li > <h3>Tasklist</h3> </li>
<li> <h3>Code Editor</h3></li>
<li> <h3>Text Editor/Html Converter</h3></li>
</ul>
</div>

It should open up with presenting the title of the program, prompting the user to scroll down to get to the main components. I added some parallax styling because I felt that it would work well with the design I was going for. 

## Tasklist

The leftmost console is a tasklist that lets the user note down and track their progress as they build out a program. You can add a task using the button and remove a task by simply clicking on any of the tasks on the list. The task gets posted to a database that loads along with the website. This way all your tasks are saved even on refreshing the page. 

I built this Tasklist in the following components

<div style='text-align:center;'>
<ul style='display:inline-block ; text-align:left ;color:turquoise'>
<li > 
<h3>App</h3> 
<ul>
<li> <h3>CodeNotes</h3></li>
<ul>
<li><h3>TaskList</h3></li>
<ul>
<li><h3>Task</h3></li>
</ul>
<li><h3>NewTaskList</h3></li>
</ul>
</ul>
</li>
</ul>
</div>

I wanted to handle all my code that had to do with writing and posting tasks tucked away under one component so I did just that with CodeNotes. 

```javascript
function CodeNotes() {
    const [tasks, setTask] = useState([]);

    useEffect(() => {
        fetch("http://localhost:4000/tasks")
            .then((r) => r.json())
            .then((notes) => setTask(notes));
    }, []);

    function handleAddTask(newtask) {
        return setTask([...tasks, newtask])
        // console.log(tasks)
    }
    function handleDeleteTask(deletedItem) {
        const updatedItems = tasks.filter((task) => task.id !== deletedItem.id);
        setTask(updatedItems);
    }

    return (
        <div>
            <TaskList tasks={tasks} deleteTask={handleDeleteTask} />
            <NewTaskList tasks={tasks} text={tasks.text} handleAddTask={handleAddTask} />
        </div>
    )
}
export default CodeNotes;
```
The first thing you'll notice here is the fetch request I'm making to get the data for my tasks from a predefined server. I then load the data that's already a json into my setter function. I then coded the <span style='color:turquoise'>handleAddTask</span> and <span style='color:turquoise'>handleDeleteTask</span> functions that will be handling the <span style='color:turquoise'>POST</span> and <span style='color:turquoise'>DELETE</span> requests I wrote in the child components.    
## Code Editor

This was a fun little project I did for fun and decided to incorporate it into my app. I wrote the whole thing in <span style='color:turquoise'>index.html</span>. This part is just a test bed to play with code superficially. You can render html, css and code javascript to interact with the content in the iframe. 
```html
<div class='codeeditor'>
    <textarea id='html-code'>HTML code... </textarea>
    <textarea id='css-code'>Css Code... </textarea>
    <textarea id='js-code'>Javascript Code...</textarea>
    <div id='iframe'>
      <iframe id='output'></iframe>
    </div>
  </div>
```
I started by first creating the textboxes for each, along with an iframe as listed above.
```html
  <script type='text/javascript'>
    function run() {
      let htmlCode = document.querySelector('.codeeditor #html-code').value;
      let cssCode = "<style>" + document.querySelector('.codeeditor #css-code').value + "</style>";
      let jsCode = document.querySelector('.codeeditor #js-code').value;
      let output = document.querySelector('.codeeditor #output');

      output.contentDocument.body.innerHTML = htmlCode + cssCode;
      output.contentWindow.eval(jsCode);
    }
    document.querySelector('.codeeditor #html-code').addEventListener('keyup', run);
    document.querySelector('.codeeditor #css-code').addEventListener('keyup', run);
    document.querySelector('.codeeditor #js-code').addEventListener('keyup', run);
  </script>
```
Under it I wrote up a function assigning the values of textboxes to variables and essentially running the text I input through the browser console but outputting into the iframe. Since I wrote this out on the html file, it serves as the center console of my app and doesn't get affected by parallax scrolling. 

## Text Editor/Html Converter
For this part I downloaded and used the following webpak: <a href='https://jpuri.github.io/react-draft-wysiwyg/#/' style='color:turquoise'>
https://jpuri.github.io/react-draft-wysiwyg/#/</a>

It didn't take too much debugging on my end, but in short I opted to use their controlled editor using EditorState with the following code in a component of its own:

```javascript
class TextEditor extends Component {
    constructor(props) {
        super(props);
        this.state = {
            editorState: EditorState.createEmpty(), };
    }
    onEditorStateChange: Function = (editorState) => {
        this.setState({
            editorState,});
    };
    render() {
        const { editorState } = this.state;
        return (
            <div>
                <Editor
                    editorState={editorState}
                    onEditorStateChange={this.onEditorStateChange}
                    wrapperClassName="wrapper-class"
                    editorClassName="editor-class"
                    toolbarClassName="toolbar-class"/>
                <textarea id='htmltxtarea' disable value={
                    draftToHtml(convertToRaw(editorState.getCurrentContent())}></textarea>
            </div>
        )}}
export default TextEditor;
```
Towards the end of my site you'll see a lofi image of myself, which I generated using: 
<a href='https://lofirecords.com/pages/lofigirl-generator
' style='color:turquoise'>
https://lofirecords.com/pages/lofigirl-generator</a>

And added music by linking a youtube video to an iframe element and setting its height in css to 0. I then linked a play and stop button that starts and stops the video so it looks like you can toggle music on and off. 

<br><br>
<p><img src="{{site.url }}{{site.baseurl}}/src/assets/img/postpix/lofi_generator w headphones.png" alt="lofi" /></p>
