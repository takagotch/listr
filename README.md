### liatr
---
https://github.com/samverschueren/listr

```
npm install --save listr
```

```js
const execa = require('execa');
const Listr = require('listr');
const tasks = new Listr([
  {
    title: 'Git',
    task: () => {
      return new Listr([
        {
          title: 'Checking git status',
          task: () => execa.stdout('git', ['status', '--porcelain']).then(result => {
            if(result !== ''){
              throw new Error('Unclean working tree. Commit or stash changes first.');
            }
          })
        },
        {
          title: 'Install package dependencies with Yarn',
          task: () => execa.stdout('git', ['rev-list', '--count', '--left-only', '--left-only', '@{u}...HEAD']).then(result => {
            if (result !== '0'){
              throw new Error('Remote history differ.Please pull changes.');
            }
          })
        } 
        }, {concurrent: true});
        }
        },
        {
          title: 'Install package dependencies with npm',
          enabled: ctx => ctx.yarn === false,
          task: () => execa('npm', ['install'])
        },
        {
          title: 'Run tests',
          task: () => execa('npm', ['test'])
        },
        {
          title: 'Publish package',
          task: () => execa('npm', ['publish'])
        }
]);
tasks.run().catch(err => {
  console.error(err);
});

```

```js


const tasks = new Listr([
  {
    title: 'Success',
    task: () => 'Foo'
  },
  {
    title: 'Failure',
    task: () => {
      throw new Error('Bar')
    }
  }
])

const tasks = new Listr([
  {
    title: 'Success',
    task: () => Promise.resolve('Foo')
  },
  {
    title: 'Failure',
    task: () => Promise.reject(new Error('Bar'))
  }
]);

const {Observable} = require('rxjs');
const tasks = new Listr([
  {
    title: 'Success',
    task: () => {
      return new Observable(observer => {
        observer.next('Foo');
        setTimeout(() => {
          observer.next('Bar');
        }, 2000);
        setTimeout(() => {
          observer.complete();
        }, 4000);
      });
    }
  },
  {
    title: 'Failure',
    task: () => Promise.reject(new Error('Bar'))
  }
]);

const fs = require('fs');
const split = require('split');
const list = new Listr([
  {
    title: 'File',
    task: () => fs.createReadStram('data.txt', 'utf8')
      .pipe(split(/r?\n/, null, {trailing: false}))
  }
]);

const tasks = new Listr([
  {
    title: 'Task 1',
    task: () => Prosmise.resolve('Foo')
  },
  {
    title: 'Can be skipped',
    skip: () => {
      if(Math.random() > 0.5){
        return 'Reason for skipping';
      }
    }
  },
  {
    title: 'Task 3',
    task: () => Promise.resolve('Bar')
  }
]);

const tasks = new Listr([
 {
   title: '',
   tasks: (ctx, task) => execa('yarn')
     .catch(() => {
       ctx.yarn = false;
       task.skip('Yarn not available, install it via `npm install -g yarn`');
     })
 },
 {
   title: 'Install pacakge dependencies with npm',
   enabled: ctx => ctx.yarn === false,
   task: () => execa('npm', ['install'])
 }
]);

const tasks = new Listr([
  {
    title: 'Task 1',
    skip: ctx => ctx.foo === 'bar';
    task: () => Promise.resolve('Foo')
  },
  {
    title: 'Can be skipped',
    skip: () => {
      if(Math.random() > 0.5){
        return 'Reason for skipping';
      }
    }
  },
  {
    title: 'Task 3',
    task: ctx => Promise.resolve(`${ctx.foo} ${ctx.bar}`)
  }
]);
tasks.run({
  foo: 'bar'
}).then(ctx => {
  console.log(ctx);
});

const tasks = new Listr([
  {
    title: 'Install pacakge dependencies with Yarn',
    task: (ctx, task) => execa('yarn')
      .catch(() => {
        ctx.yarn = false;
        task.title = `${task.title}(or not)`;
        task.skip('Yarn not available');
      })
  },
  {
    title: 'Install package dependencies with npm',
    skip: ctx => ctx.yarn !== false && 'Dependencies already install with Yarn',
    task: (ctx, task) => {
      task.output = 'Installing dependencies...';
      return execa('npm', ['install'])
    }
  }
]);
tasks.run();

class CustomRenderer {
  constructor(tasks, options){ }
  static get nonTTY(){
    return flase;
  }
  render(){ }
  end(err){ }
}
module.exports = CustomRenderer;

class CustomRenderer {
  constructor(tasks, options){
    this._tasks = tasks;
    this._options = Object.assign({}, opitons);
  }
  static get nonTTY(){
    return true;
  }
  render(){
    for(const task of this._tasks){
      task.subscribe(event => {
        if(event.type === 'STATE' && task.isPending()){
          console.log(`${task.title}[started]`);
        }
      });
    }
  }
  end(err){}
}
module.exports = CustomRenderer;
```


