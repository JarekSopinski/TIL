## Builder

### Definition

When piecewise object construction is complicated, provide an API for doing it succinctly.

### Motivation

- Some objects are simple and can be created in a single initializer call.
- Other objects require a lot of ceremony to create. But having an object with i.e. 10 initializer arguments is not productive.
- Instead, opt for piecewise construction.
- Builder provided an API for constructing an object step-by-step.

Builder is a component that helps you figure out the whole process of building complicated objects (which are impossible to construct i.e. by using a single line constructor call). You want to make a process of building a single object understable and easy to use.

### Additional notes

- We can either give builder an initializer or return it via a static function.
- To make builder fluent, return this in each method.
- Different facets on an object can be built with different builders (sub-builders) working in tandem via a base class (parent builder).

### Example

Example of implementing the Builder design pattern for rendering simple chunks of code.

    class Field
    {
      constructor(name)
      {
        this.name = name;
      }
    }
    
    class Class
    {
      constructor(name)
      {
        this.name = name;
        this.fields = [];
      }
    
      toString()
      {
        let buffer = [];
        buffer.push(`class ${this.name} {\n`);
    
        if (this.fields.length > 0) {
          buffer.push(`  constructor(`);
          for (let i = 0; i < this.fields.length; ++i) {
            buffer.push(this.fields[i].name);
            if (i + 1 !== this.fields.length)
              buffer.push(', ');
          }
          buffer.push(`) {\n`);
          for (let field of this.fields) {
            buffer.push(`    this.${field.name} = ${field.name};\n`);
          }
          buffer.push('  }\n');
        }
    
        buffer.push('}');
        return buffer.join('');
      }
    }
    
    class CodeBuilder
    {
      constructor(className)
      {
        this._class = new Class(className);
      }
    
      addField(name)
      {
        this._class.fields.push(
          new Field(name)
        );
        return this;
      }
    
      toString()
      {
        return this._class.toString();
      }
    }
    
    let cb = new CodeBuilder('Person');
    cb.addField('name').addField('age');
    console.log(cb.toString());