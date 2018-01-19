> React.PropTypes从15.5开始移动到单独的prop-types库里

## 使用方法
### 普通组件
不使用context

	class Child extends React.Component {
        render() {
            return (
                <div>
                    这是来自领导{this.props.leader}的命令
                </div>
            );
        }
    }
    class Mother extends React.Component {
        render() {
            return (
                <div>
                    <Child leader={this.props.parent}/>
                </div>
            );
        }
    }
    class Grandpa extends React.Component {
        render() {
            return (
                <div>
                    <Mother parent="kangkang"/>
                </div>
            );
        }
    }
使用Context

	const PropTypes = require('prop-types');
	class Child extends React.Component {
        render() {
            return (
                <div>
                    这是来自领导{this.context.leader}的命令
                </div>
            );
        }
    }
    Child.contextTypes = {
        leader: PropTypes.string
    }
    class Mother extends React.Component {
        render() {
            return (
                <div>
                    <Child/>
                </div>
            );
        }
    }
    class Grandpa extends React.Component {
        getChildContext() {
        	  return {
        	      parent: 'kangkang'
        	  };
        }
        render() {
            return (
                <div>
                    <Mother/>
                </div>
            );
        }
    }
    Grandpa.childContextTypes = {
        parent: PropTypes.string
    };
### 无状态组件
    const PropTypes = require('prop-types');
    const Child = ({}, context) => {
        render() {
            return (
                <div>
                    这是来自领导{this.context.leader}的命令
                </div>
            );
        }
    }
    Child.contextTypes = {
        leader: PropTypes.string
    }
## 使用了contextTypes组件的生命周期
如果使用了contextTypes，那么一个组件的生命周期函数会接受到新的参数

	constructor(props, context)
	componentWillReceiveProps(nextProps, nextContext)
	shouldComponentUpdate(nextProps, nextState, nextContext)
	componentWillUpdate(nextProps, nextState, nextContext)
	componentDidUpdate(nextProps, nextState, nextContext)
## 更新context
当state或者prop更新时，getChildContext方法会被调用，子组件也会触发更新，但是如果子组件的shouldComponentUpdate方法返回false，接下来的子组件不会更新。
