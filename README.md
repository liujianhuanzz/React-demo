# React-demo

React是Facebook开源的JavaScript库，用于构建UI。在React里可以传递多种类型的参数，如声明代码，帮助渲染出UI、也可以是静态的HTML DOM元素、也可以传递动态变量、甚至是可交互的应用组件。

demo1：HTML模板和React.render()

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8">
        <title>React demo1</title>
    <script type="text/javascript" src="../lib/JSXTransformer.js"></script>
    <script type="text/javascript" src="../lib/react.js"></script>
    </head>
    <body>
    <div id="example"></div>
    <script type="text/jsx">
    React.render(
        <h1>Hello, world!</h1>,
        document.getElementById('example')
    );  
    </script>
    </body>
    </html>

1.最后一个script标签的type属性为text/jsx。这是React独有的JSX语法,跟JavaScript不兼容。凡是使用JSX的地方,都要加上type="text/jsx".
2.React提供两个库：react.js和JSXTransformer.js，它们必须首先加载。其中，JSXTransformer.js的作用将JSX语法 转化为JavaScript语法。
3.React.render是React的最基本方法，用于将模板转为HTML语言，并插入指定的DOM节点。

demo2:JSX语法

HTML语言直接写在JavaScript语言之中，不加任何引号，这就是JSX语法，它允许HTML与JavaScript的混写。

    var names = ['Alice','Emily','Kate'];
    React.render(
        <div>
        {
            names.map(function(name){
                return <div>Hello, {name}!</div>
            })
        }
        </div>,
        document.getElementById('example')
    );

上面代码体现了JSX的基本语法规则：遇到HTML标签（以<开头）,就用HTML规则解析; 遇到代码块（以｛开头）,就用JavaScript规则解析。

JSX允许直接在模板插入JavaScript变量。如果这个变量是一个数组，则会展开这个数组的所有成员。

    var arr = [
            <h1>Hello world!</h1>,
            <h2>React is awesome</h2>,
            ];
    React.render(
        <div>{arr}</div>,
        document.getElementById('example')
    );


上面代码的arr变量是一个数组,JSX会把它的所有成员,添加到模板。

demo3:组件

React允许将代码封装成组件`component`，然后像插入普通HTML标签一样，在网页中插入这个组件。`React.createClass`方法就用于生成一个组件类。

    var HelloMessage = React.createClass({
        render:function(){
            return <h1>Hello {this.props.name}</h1>;
        }
    });
    React.render(
        <HelloMessage name="huanhuan"/>,
        document.getElementById('example')
    );

上面代码中，变量`HelloMessage`就是一个组件类。模板插入`<HelloMessage />`时会自动生成`HelloMessage`的一个实例。所有组件类都必须有自己的`render`方法，用于输出组件。组件的用法与原生的HTML标签完全一致，可以任意加入属性，比如`<HelloMessage />`,就是`HelloMessage`组件加入一个`name`属性，值为huanhuan.添加组件属性有一个地方需要注意，就是`class`属性需要写成`className`，`for`属性需要写成`htmlFor`，这是因为`class`和`for`是JavaScript的保留字。

demo4:this.props.children

`this.props`对象的属性与组件的属性一一对应，但是有一个例外，就是`this.props.children`属性。它表示组件的所有子节点。

    var NotesList = React.createClass({
        render:function(){
            return (
                <ol>
                {
                    this.props.children.map(function(child){
                        return <li>{child}</li>
                    })
                }
                </ol>
            );
        }
    });
    
    React.render(
        <NotesList>
            <span>hello</span>
            <span>world</span>
        </NotesList>,
        document.getElementById('example')
    );

demo5: React.findDOMNode()

组件并不是真实的DOM节点，而是存在于内存之中的一种数据结构，叫做**虚拟DOM（virtual DOM）**。只有当它插入文档以后，才会变成真实的DOM。根据React的设计，所有的DOM变动，都先在虚拟DOM上发生，然后再将实际发生变动的部分，反映在真实DOM上，这种算法叫做**DOM diff**，它可以极大提高网页的性能表现。
但是，有时需要从组件获取真实DOM的节点，这时就要用到`React.findDOMNode`方法。

    var MyComponent = React.createClass({
        handleClick:function(){
            React.findDOMNode(this.refs.myTextInput).focus();
        },
        render:function(){
            return (
                <div>
                    <input type="text" ref="myTextInput" />
                    <input type="button" value="Focus the text input" onClick={this.handleClick}/>
                </div>
            );
        }
    });
    
    React.render(
        <MyComponent />,
        document.getElementById('example')
    );

上面代码中，组件MyComponent的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的DOM节点，虚拟DOM是拿不到用户输入的。为了做到这一点，文本输入框必须有一个ref属性，然后`this.refs.[refName]`就指向这个虚拟DOM的子节点，最后通过`React.findDOMNode`方法获取真实DOM的节点。需要注意的是，由于`React.findDOMNode`方法获取的是真实DOM，所以必须等到虚拟DOM插入文档以后才能调用这个方法，否则会返回null。上面代码中，通过为组件指定Click事件的回调函数，确保了只有等到真实DOM发生Click事件之后，才会调用`React.findDOMNode`方法。

demo6: this.state

组件免不了要与用户互动，React的一大创新，就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染UI。

    var LikeButton = React.createClass({
        getInitialState:function(){
            return {liked:false};
        },
        handleClick:function(event){
            this.setState({liked: !this.state.liked});
        },
        render:function(){
            var text = this.state.liked ? 'like' : 'haven\'t liked';
            return (
                <p onClick={this.handleClick}>
                    You {text} this.Click to toggle.
                </p>
            ); 
        }
    });
    
    React.render(
        <LikeButton />,
        document.getElementById('example')
    );

上面代码是一个`LikeButton`组件，它的`getInitialState`方法用于定义初始状态，也就是一个对象，这个对象可以通过`this.state`属性读取。当用户点击组件，导致状态变化，`this.setState`方法就修改状态值，每次修改以后，自动调用`this.render`方法，再次渲染组件。
由于`this.props`和`this.state`都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，`this.props`表示那些一旦定义，就不再改变的特性，而`this.state`是会随着用户互动而产生变化的特性。

demo7: 表单

用户在表单填入的内容，属于用户跟组件的互动，所以不能用`this.props`读取。

    var Input = React.createClass({
        getInitialState:function(){
            return {value:'Hello!'};
        },
        handleChange:function(event){
            this.setState({value:event.target.value});
        },
        render:function(){
            var value = this.state.value;
            return (
                <div>
                    <input type="text" value={value} onChange={this.handleChange} />
                    <p>{value}</p>
                </div>
            );
        }
    });
    React.render(<Input />,document.getElementById('example'));

上面代码中，文本输入框的值，不能用`this.props.value`读取，而要定义一个`onChange`事件的回调函数，通过`event.target.value`读取用户输入的值。`textarea元素、select元素、radio元素`都属于这种情况。

demo8: 组件的生命周期

组件的生命周期分成三个状态：

 - Mounting:已插入真实DOM
 - Updating：正在被重新渲染
 - Unmounting：已移出真实DOM

React为每个状态都提出了两种处理函数，will函数在进入状态之前调用，did函数在进入状态之后调用，三种状态共计五种处理函数：

 - componentWillMount()
 - componentDidMount()渲染到dom树中时调用，可用于获取原生节点
 - componentWillUpdate(object nextProps, object nextState)
 - componentDidUpdate(object prevProps, object prevState)
 - componentWillUnmount()组件从dom销毁前调用


此外，React还提供两种特殊状态的处理函数

 - componentWillReceiveProps(object nextProps):已加载组件收到新的参数时调用
 - shouldComponentUpdate(object nextProps,object nextState):组件判断是否重新渲染时调用
 
----------
     var Hello = React.createClass({
            getInitialState:function(){
                return {opacity:1.0};
            },
            componentDidMount:function(){
                this.timer = setInterval(function(){
                    var opacity = this.state.opacity;
                    opacity -= .05;
                    if(opacity < 0.1){
                        opacity = 1.0;
                    }
                    this.setState({
                        opacity:opacity
                    });
                }.bind(this),100);
            },
            render:function(){
                return (
                    <div style={{opacity:this.state.opacity}}>
                        Hello {this.props.name}
                    </div>
                );
            }
        });
        
        React.render(
            <Hello name="world" />,
            document.getElementById('example')
        );

上面代码在hello组件加载以后，通过`componentDidMount`方法设置一个定时器每隔100毫秒，就重新设置组件的透明度，从而引发重新渲染。
另外，组件的style属性的设置方式也值得注意，不能写成


    style="opacity:{this.state.opacity};"

而要写成

    style={{opacity:this.state.opacity}}

这是因为 React组件样式 是一个对象，所以第一重大括号表示这是JavaScript，第二重大括号表示样式对象。

demo9: Ajax

组件的数据来源通常是通过Ajax请求从服务器获取，可以使用componentDidMount方法设置Ajax请求，等到请求成功，再用this.setState来重新渲染UI。
```javascript
var UserGist = React.createClass({
    getInitialState:function(){
        return {
            username:'',
            lastGistUrl:''
        };
    },
    componentDidMount:function(){
        $.get(this.props.source,function(result){
            var lastGist = result[0];
            if(this.isMounted()){
                this.state({
                    username:lastGist.owner.login,
                    lastGistUrl:lastGist.html_url
                });
            }
        }.bind(this));
    },
    render:function(){
        return (
            <div>
                {this.state.username} s last gist is
                <a href={this.state.lastGistUrl}>here</a>.
            </div>
        );
    }
});

React.render({
    <UserGist source="https://api.github.com/users/octocat/gists" />,
    document.getElementById('example')
});
```
demo10:组件嵌套

```javascript
var Search = React.createClass({
    render:function(){
        return (
            <div>
                {this.props.searchType}:<input type="text" />
                <button>Search</button>
            </div>
        );
    }
});

var Page = React.createClass({
    render:function(){
        return (
            <div>
                <h1>Welcome!</h1>
                <Search searchType = 'Title' />
                <Search searchType = 'Content' />
            </div>
        );
    }
});

React.render(<Page />,document.getElementById('example'));
```

demo11: mixin

mixin是一个普通对象，通过mixin可以在不同组件之间共享代码
```javascript
var mixin = {
    propTypes: {
        title: React.PropTypes.string,
    },

    getDefaultProps(){
        return {
            title:'default'
        };
    },
};

var A = React.createClass({
    mixins: [mixin],
    render(){
        return <i>{this.props.title}</i>
    }
});

var B = React.createClass({
    mixins: [mixin],
    render(){
        return <b>{this.props.title}</b>
    }
});

React.render(<div><B/><A title={2}/><A/></div>,
    document.getElementById('example'));
```

demo12: FORM

和html的不同点：

 - value/checked属性设置后，用户输入无效
 - textarea的值要设置在value属性上
 - select的value属性可以是数组，不建议使用option的selected属性
 - input/textarea的onChange用户每次输入都会触发
 - radio/checkbox点击后也会触发onChange

如果设置了value属性，组件变为受控组件，用户无法输入，除非程序改变value属性
```javascript
var Test = React.createClass({
    render(){
        return <input value="x" />
    }
});

React.render(<Test />,document.getElementById('example'));
```
受控组件可以通过监听onChange事件结合state来改变input的值

```javascript
var Test = React.createClass({
    getInitialState(){
        return {
            value:'xasdasdf'
        };
    },
    onChange(e){
        this.setState({
            value:e.target.value
        });
    },
    render(){
        return <input value={this.state.value} onChange={this.onChange}/>
    }
});

React.render(<Test />,document.getElementById('example'));
```
demo13:REF

该功能是为了结合现有非React类库，通过ref/refs可以获取得到组件实例，进而取得原生节点。

```javascript
var Test = React.createClass({
    componentDidMount(){
      alert(React.findDOMNode(this.refs.content).innerHTML);
    },
    render(){
        return <div>
            <h3>header</h3>
            <div ref='content'>content</div>
        </div>;
    }
});

React.render(<Test />,document.getElementById('example'));
```
demo14: 一个基于React的简易评论应用

组件分解：

```
顶层CommentBox
    评论列表CommentList
        单条评论Comment
    评论表单CommentForm
```

```javascript
var data = [
  {author: "作者 1", text: "评论 1"},
  {author: "作者 2", text: "*评论 2*"}
];

var Comment = React.createClass({
    render:function(){
        return (
            <div className="comment">
                <h2 className = 'commentAuthor'>
                    {this.props.author}
                </h2>
                {this.props.children}
            </div>
        );
    }
});

var CommentList = React.createClass({
  render: function() {
    var commentNodes = this.props.data.map(function (comment) {
      return (
        <Comment author={comment.author}>
          {comment.text}
        </Comment>
      );
    });
    return (
      <div className="commentList">
        {commentNodes}
      </div>
    );
  }
});

var CommentForm = React.createClass({
  render: function() {
    return (
      <div className="commentForm">
        Hello world! I am a CommentForm.
      </div>
    );
  }
});
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        <h1>Comments</h1>
        <CommentList data={data}/>
        <CommentForm />
      </div>
    );
  }
});
React.render(<CommentBox />,document.getElementById('example'));
```

demo15:一个基于React的学生成绩表，支持按性别和姓名筛选

StudentScoreTable有三个子组件：GenderFilter, NameFilter, ScoreTable。其中，ScoreTable又包含若干个子组件ScoreItem。从功能上：

 - 当选择性别后，要对ScoreItem做筛选
 - 当输入姓名后，要对ScoreItem做筛选
 - 当同时选择性别，输入姓名，两个筛选条件对ScoreItem生效
 - 点击某ScoreItem的删除按钮后，删除此ScoreItem

也就是说：

 - 当GenderFilter发生改变（change）时，ScoreItem要能感知到这个变化并做出相应反馈（进行筛选）
 - 当NameFilter发生改变时，ScoreItem也要能感知这个变化并做出相应反馈
 - 当某ScoreItem删除后，可能StudentScoreTable, GenderFilter, NameFilter要做出相应反馈，即以后筛选，少了一个ScoreItem

```javascript
var _score = [
    {name:"张三",gender:"男",chinese:85,math:98,_id:0},
    {name:"张三",gender:"女",chinese:95,math:90,_id:1},
    {name:"李四",gender:"男",chinese:65,math:48,_id:2},
    {name:"大妹",gender:"女",chinese:95,math:100,_id:3},
    {name:"王五",gender:"男",chinese:75,math:88,_id:4},
    {name:"赵钱",gender:"男",chinese:75,math:98,_id:5},
    {name:"二妹",gender:"女",chinese:90,math:98,_id:6},
];

var StudentScoreTable = React.createClass({
    getInitialState:function(){
        return {
            genderFilter:0,
            nameFilter:'',
            data:_score,
            modifyScore:null,
            className:'dialog modify'
        };
    },
    onGenderChange:function(gender){
        this.setState({genderFilter:gender});
    },
    onDeleteScoreItem:function(id){
        var data = this.state.data.map(function(item){
            if(item._id === id){
                item.delteFlag = true;
            }
            return item;
        });
        this.setState({data:data});
    },
    onNameChange:function(name){
        this.setState({nameFilter:name});
    },
    render:function(){
        return (
            <div>
                <GenderFilter onGenderChange={this.onGenderChange} genderFilter={this.state.genderFilter} />
                <NameFilter onNameChange={this.onNameChange} nameFilter={this.state.nameFilter}/>
                <ScoreTable 
                    scoreNotes = {this.state.data}
                    genderFilter = {this.state.genderFilter}
                    nameFilter = {this.state.nameFilter}
                    deleteScoreItem = {this.onDeleteScoreItem}
                    modifyItem = {this.onModify}
                />
            </div>
        );
    }
});

var GenderFilter = React.createClass({
    genderChangeHandler:function(){
        this.props.onGenderChange(this.refs.genderFilter.getDOMNode().value);
    },
    render:function(){
        return (
            <div className="condition-item">
                <label>
                    <span>按性别筛选</span>
                    <select onChange={this.genderChangeHandler} ref="genderFilter">
                        <option value="0">All</option>
                        <option value="1">男生</option>
                        <option value="2">女生</option>
                    </select>
                </label>
            </div>
        );
    }
});

var NameFilter = React.createClass({
    nameChangeHandler:function(){
        this.props.onNameChange(this.refs.nameFilter.getDOMNode().value);
    },
    render:function(){
        return (
            <div className="condition-item">
                <label>
                    <span>按姓名筛选</span>
                    <input type="text" ref="nameFilter" onChange={this.nameChangeHandler}/>
                </label>
            </div>
        );
    }
});

var ScoreTable = React.createClass({
    deleteItemHandler:function(id){
        this.props.deleteScoreItem(id);
    },
    modifyItem:function(id){
        this.props.modifyItem(id);
    },
    render:function(){
        var scoreNotes = [];
        var genderFilter = this.props.genderFilter,
            nameFilter = this.props.nameFilter,
            GENDER = ['','男','女'],
            _this = this;

        this.props.scoreNotes.map(function(scoreItem){
            if(genderFilter !== 0 && nameFilter === ''){
                if(GENDER[genderFilter] === scoreItem.gender){
                    !scoreItem.delteFlag && scoreNotes.push(<ScoreItem score={scoreItem} onDelete={_this.deleteItemHandler} onModify={_this.modifyItem} />);
                }
                return ;
            }
            if(genderFilter === 0 && nameFilter !== ''){
                if(scoreItem.name === nameFilter){
                    !scoreItem.delteFlag && scoreNotes.push(<ScoreItem score={scoreItem} onDelete={_this.deleteItemHandler} onModify={_this.modifyItem} />);
                }
                return ;
            }
            if(genderFilter !== 0 && nameFilter !== ''){
                if(GENDER[genderFilter] === scoreItem.gender && scoreItem.name === nameFilter){
                    !scoreItem.delteFlag && scoreNotes.push(<ScoreItem score={scoreItem} onDelete={_this.deleteItemHandler} onModify={_this.modifyItem} />);
                }
                return;
            } 
            !scoreItem.delteFlag && scoreNotes.push(<ScoreItem score={scoreItem} onDelete={_this.deleteItemHandler} onModify={_this.modifyItem} />);
        });

        return (
            <table>
                <thead>
                    <tr>
                        <th>姓名</th>
                        <th>性别</th>
                        <th>语文</th>
                        <th>数学</th>
                        <th>操作</th>
                    </tr>
                </thead>
                <tbody>
                    {scoreNotes}
                </tbody>
            </table>
        );
    }
});

var ScoreItem = React.createClass({
    deleteHandler:function(e,id){
        this.props.onDelete(this.props.score._id);
    },
    modifyHandler:function(){
        this.props.onModify(this.props.score._id);
    },
    render:function(){
        var score = this.props.score;

        return (
            <tr>
               <td>{score.name}</td>
               <td>{score.gender}</td>
               <td>{score.chinese}</td>
               <td>{score.math}</td>
               <td><span className="trigger" onClick={this.modifyHandler}>修改</span>　<span className="trigger" onClick={this.deleteHandler}>删除</span></td>
            </tr>
        );
    }
});

React.render(<StudentScoreTable />,document.getElementById('example'));
```