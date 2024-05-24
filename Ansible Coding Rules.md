Below is the style guide I use when writing ansible code.  Not all the rules are best practices from ansible, but they are what work for me.  

Additionally, all code should be run thru ansible-lint and have it pass (example ansible-lint config in this directory)

### Note, not all best practices are applied to each example for ease of comprehension. All rules still apply, they just may not be illustrated.

## Basic Rules
- All tasks need a name.  This makes output far more useful, and easier to search/parse.
  
  Bad Code:
  ```yaml
  - ansible.builtin.set_fact:
      foo: bar
  ```
  Good Code:
  ```yaml
  - name: Set needed variable
    ansible.builtin.set_fact:
      foo: bar
  ```
- All tasks need to use FQDN for modules.  This ensure that the correct module is always used.

  Bad Code:
  ```yaml
  - command: foo
  ```
  Good Code:
  ```yaml
  - ansible.builtin.command: foo
  ```

- Whenever possible, use a native ansible module.  This provides a more flexable way of doing things, and OS changes are unlikely to change things

  Bad Code:
  ```yaml
  - ansible.builtin.command: apt-get update
  ```
  Good Code:
  ```yaml
  - ansible.builtit.apt:
      update_cache: true
  ```
    
- If errors are ignored, add a comment explaining why.  This just prevents bad practices, and adds to future code maintance

  Bad Code:
  ```yaml
  - ansible.builtin.command: foo
    ignore_errors: true
  ```
  Good Code:
  ```yaml
  # This command will fail, but in a silent and non-breaking way
  - ansible.builtin.command: foo
    ignore_errors: true
  ```
- When setting bool values, always use true/false.  This ensures consistency, especially when others are using/modifying your code

  Bad Code:
  ```yaml
  - ansible.builtin.set_fact:
      foo: yes
  ```
  Good Code:
  ```yaml
  - ansible.builtin.set_fact:
      foo: true
  ```

- Use propper spacing for jinja2.  This ensures consistency, especially when others are using/modifying your code

  Bad Code:
  ```yaml
  - ansible.builtin.set_fact:
      foo: "{{ bar [-1] }}"

  - ansible.builtin.set_fact:
      foo: "{{bar[-1]}}"
  ```
  Good Code:
  ```yaml
  - ansible.builtin.set_fact:
      foo: "{{ bar[-1] }}"
  ```

- Add a blank line between multiple tasks.  This ensures consistency, and makes it far easier to read.

  Bad Code:
  ```yaml
  - ansible.builtin.command: foo
  - ansible.builtin.command: bar
  ```
  Good Code:
  ```yaml
  - ansible.builtin.command: foo

  - ansible.builtin.command: bar
  ```
## Block Rules
- When using a block, put when before the tasks.  This makes block logic easier to read.

  Bad Code:
  ```yaml
  - block:
      - ansible.builtin.command: foo

      - ansible.builtin.command: bar
    when: foo == bar
  ```
  Good Code:
  ```yaml
  - block:
    when: foo == bar
      - ansible.builtin.command: foo

      - ansible.builtin.command: bar
  ```

- When using blocks, group common whens.  This ensures consitency and reduces redundancy.

  Bad Code:
  ```yaml
  - block:
      - ansible.builtin.command: foo
        when: foo == bar

      - ansible.builtin.command: bar
        when: foo == bar
  ```
  Good Code:
  ```yaml
  - block:
    when: foo == bar
      - ansible.builtin.command: foo

      - ansible.builtin.command: bar
  ```
  
## Command/Shell Rules
- Unless needed, use command instead of shell.  Shell is slower, and can cause expected results.

  Bad Code:
  ```yaml
  - ansible.builtin.shell: echo foo
  ```
  Good Code:
  ```yaml
  - ansible.builti.command: echo foo
  ```
  
- When using command/shell, include a changed_when line.  Not all commands output in a logical form when they fail.

  Bad Code:
  ```yaml
  - ansible.builtin.command: foo
  ```
  Good Code:
  ```yaml
  - ansible.builtin.command: foo
    register: foo_out
    changed_when: "'bar' not in foo_out.stdout"

  - ansible.builtin.command: foo
    register: foo_out
    changed_when: foo_out.rc != 0
  ```

- When using pipes, use pipefail whenever possible.  Not using this can cause some very unexpected and unwanted failures.

  Bad Code:
  ```yaml
  - ansible.builtin.shell: false | cat
  ```

   Good Code:
  ```yaml
  - ansible.builtin.shell: set -o pipefail && false | cat
  ```

- When using command/shell, dont force a good as opposed to handling errors.

  Bad Code:
  ```yaml
  - ansible.builtin.command: foo ; echo 0
  ```

  Good Code:
  ```yaml
  - ansible.builtin.command: foo
    register: foo_out
    failed_when: ( foo_out.rc not in [ 0, 1 ])

  - ansible.builtin.command: foo
    ignore_errors: true
  ``` 
