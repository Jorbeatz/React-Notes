# React Testing
*	How to setup an automated test suite to verify that the entire application works

## Setting up Jest
*	Add it in via yarn locally: ```yarn add jest```
* 	You can then add a script in the *package.json* file
	
```
"test": "jest"
```

* 	Yarn has a special alias so that you don't have to use "yarn run test" but instead can just do "yarn test"

## Test Files
*	All test files needs a **.test** extenstion like **add.test.js**

## Simple Test Function
```
const add = (a, b) => a + b;

test('should add two numbers', () => {
	const result = add(3, 4);
	expect(result).toBe(7);

});
```

## Testing Action Generators
*	Clone the folders inside src into the test folder
* 	Exact same except with **.test.js** extension
*  	If we check if an object/array is equal, it will always be false. We have check its properties.
*  	We want to use toEqual for objects/arrays and toBe for numbers and strings


### Example Tests for Action Generators

#### Provided Values

```
test('should setup add expense action object with provided values', () => {
	const expenseData = {
		description: 'Rent',
		amount: 1095,
		createdAt: 1000,
		note: 'last month pay'
	};
	const action = addExpense(expenseData);
	expect(action).toEqual({
		type: 'ADD_EXPENSE',
		expense: {
			...expenseData,
			id: expect.any(String)
		}
	});

});
```


#### Default Values
```
test('should setup add expense object with default values', () => {
	const action = addExpense();
	expect(action).toEqual({
		type: 'ADD_EXPENSE',
		expense: {
			id: expect.any(String),
			description: '',
			amount: 0,
			note: '',
			createdAt: 0,
		}
	});
});
```

## Testing Selectors
*	Same stuff as you can expect


```
test('should filter by endDate', () => {
	const filters = {
		text: '',
		sortBy: 'date',
		startDate: undefined,
		endDate: moment(0).add(2, 'day')
	};
	const result = selectExpenses(expenses, filters);
	expect(result).toEqual([ expenses[3], expenses[0], expenses[2], expenses[1] ]);
});

test('should sort by date', () => {
	const filters = {
		text: '',
		sortBy: 'date',
		startDate: undefined,
		endDate: undefined
	};
	const result = selectExpenses(expenses, filters);
	expect(result).toEqual([ expenses[3], expenses[0], expenses[2], expenses[1] ]);
});
```

## Snapshot Testing
*	Testing components, start by adding react-test-renderer
* 	Yeah this is just Jest's snapshot testing which renders a component and saves that rendered output and compares it to see if anything changes. You can update that snapshot but yeah, thats how it works.

## Enzyme
*	Much more full-featured renderer
* 	You need Jest to work with Enzyme first by adding a jest.config.json
*  	Modify yarn test to add config files

### Shallow Rendering
*	You can render the component and then make different assertions looking at the API as to what its supposed to be

```
const wrapper = shallow(<Header />);
expect(wrapper).toMatchSnapshot();	
```

### Enzyme to JSON
*	Library: enzyme-to-json
* 	This simplifies the snapshot with the things we need

```
expect(toJSON(wrapper)).toMatchSnapshot()
```

*	Instead of doing that all the time though, we can just configure our jest config file to do that all the time

## Snapshot testing with dynamic components
*	Export/import the unconnected componenent from mapStateToProps
* 	Same thing, import the component, generate the snapshot and test it

```
test('should render ExpenseListItem with expense', () => {
	const wrapper = shallow(<ExpenseListItem {...expenses[0]} />);
	expect(wrapper).toMatchSnapshot();
});
```

## Mocking Libraries with Jest
*	Sometimes the problem is that the library keeps changing so that you can't create a snapshot. However, you can create a file to simulate a single event and then snapshot from there.

## Testing Events
```
test('should set amount on input', () => {
	const value = '200';
	const wrapper = shallow(<ExpenseForm />);
	wrapper.find('input').at(1).simulate('change', {
		target: { value }
	});
	expect(wrapper.state('amount')).toBe(value);
```

## Test Spies 
*	Spies are pretty much fake functions. Jest has a bunch of built-in functions that you can use to test that fake function. For example:

```
test('should call onSubmit prop for valid form submission', () => {
	const onSubmitSpy = jest.fn();
	onSubmitSpy('Jords', 'Jordy');
	expect(onSubmitSpy).toHaveBeenCalled();
	expect(onSubmitSpy).toHaveBeenCalledWith('Jords', 'Jordy');
});
```

*	The following code creates a test function using ```jest.fn``` and checks whether that function was called and also what arguments that function was called with.

### How does this work?
```
test('should call onSubmit prop for valid form submission', () => {
	const onSubmitSpy = jest.fn();
	const wrapper = shallow(<ExpenseForm expense={expenses[0]} onSubmit={onSubmitSpy} />);
	wrapper.find('form').simulate('submit', {
		preventDefault: () => {}
	});
	expect(wrapper.state('error')).toBe('');
	expect(onSubmitSpy).toHaveBeenCalledWith({
		description: expenses[0].description,
		amount: expenses[0].amount,
		note: expenses[0].note,
		createdAt: expenses[0].createdAt
	});
});
```

1.	Here, we create the mock function onSubmitSpy
2. 	We create the mock component ExpenseForm passing an expense and the mock function
3. 	We simulate the onSubmit button on the mock form
4. 	We expect no errors and than we expect the mock function to return with the following things as we would the expense form to.

*	Side note is that the reason we don't just use expenses[0] and instead write out the entire expenses[0] object is because we don't expect the onSubmit to return with the ID as well.

### How does this work?

```
test('should set calendar focus on change', () => {
	const focused = true;
	const wrapper = shallow(<ExpenseForm />);
	wrapper.find('withStyles(SingleDatePicker)').prop('onFocusChange')({ focused });
	expect(wrapper.state('calendarFocused')).toEqual(true);
});
```

*	The most confusing part here is the ({ focused }) part of wrapper.find
* 	So we get the SingleDatePicker component and grab the onFocusChange part of that component. See here:

```
<SingleDatePicker
	date={this.state.createdAt}
	onDateChange={this.onDateChange}
	focused={this.state.calendarFocused}
	**onFocusChange={this.onFocusChange}**
	numberOfMonths={1}
	isOutsideRange={() => false}
/>
```

*	Then we can see this here in the function thats called

```
onFocusChange = ({ focused }) => {
	this.setState(() => ({calendarFocused: focused}));
};
```

*	It takes in a focused object which is what we pass in that second pair of arguments
* 	We do the same thing here:

```
test('should set new date on date change', () => {
	const now = moment();
	const wrapper = shallow(<ExpenseForm />);
	wrapper.find('withStyles(SingleDatePicker)').prop('onDateChange')(now);
	expect(wrapper.state('createdAt')).toEqual(now);
});
```

*	We pass in the current moment which is specified in the event handler:

```
onDateChange = (createdAt) => {
	if(createdAt) {
		this.setState(() => ({createdAt}));	
	};
};
```


