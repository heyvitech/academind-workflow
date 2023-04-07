# GitHub Actions Workflow Exercise
This exercise is from Academind's Udemy course [(GitHub Actions - The Complete Guide)](https://udemy.com/course/github-actions-the-complete-guide)

## Instructions
![Screenshot 2023-04-06 174453](https://user-images.githubusercontent.com/103162805/230339895-f2d7117b-4ec8-4bc5-af99-507c755335f6.png)
_This diagram is created by Academind_

1. Copy the files in this repo, _except_ for a folder called `.github`.

   This folder contains all of your workflow files, so  you need to create one on your own!  

2. Create TWO workflows.
   - **First workflow** should contain _lint, test_ and _deploy_ steps. Feel free to divide them into three jobs if you'd like. They should run after one another.
   - **Second workflow** should run _only when a new issue is created_, and print out the details of the issue.
   
## My Solution
I started off creating the first workflow with three jobs, namely `lint`, `test` and `deploy`. I didn't think of creating the workflow in a single job, which in this case is actually easier and less time-consuming. It occurred to me only after I watched Max's solution video.

### First workflow with single job
File name: `deployment2.yml`
```
name: Deploy Project
on: push
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install dependencies
        run: npm ci
      - name: Lint
        run: npm run lint
      - name: Test code
        run: npm run test
      - name: Build code
        run: npm run build
      - name: Deploy code
        run: echo "Deploying..."
```
See? Isn't this is short and sweet?

### First workflow with three jobs
File name: `deployment.yml`
My solution is pretty much the same as Max's:
```
name: Deploy Project
on: push
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install dependencies
        run: npm ci
      - name: Lint
        run: npm run lint

  test:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install dependencies
        run: npm ci
      - name: Run tests
        run: npm run test
  
  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Get code
        uses: actions/checkout@v3
      - name: Install dependencies
        run: npm ci
      - name: Build code
        run: npm run build
      - name: Deploy code
        run: echo "Deploying..."
```

### Second workflow
At first, after reading [GitHub's documentation on Event Triggers](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#on), I arrived at this solution which didn't work:
```
name: Handling Issues
on: 
   issues:
      types:
         - created
```
I couldn't figure out why, so I ~~cheated~~ watched the solution video and came up with solution below:

File name: `issues.yml`
```
name: Handling Issues
on: issues
jobs:
  output:
    runs-on: ubuntu-latest
    steps:
      - name: Output Issues details
        run: echo "${{ toJSON(github.event) }}"
```

## Conclusion
Overall, I find that this exercise's difficulty was just right. I like that it picked on our brain a little bit to see if we could figure out some things on our own. Definitely try this exerise out if you're a beginner in GitHub Actions.

Happy learning!