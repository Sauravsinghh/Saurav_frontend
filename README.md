# SteelEye-Assignment
DEMO - https://codesandbox.io/s/elastic-austin-0dmj00?file=/src/App.js:0-1550
```
1.	Explain what the simple List component does.
=> This code creates a React component called 'List', which receives an array of objects named 'items' as a prop. Each 'item' object must have a 'text' parameter of type string. The 'List' component iterates through the 'items' array using the 'map' function and renders a 'SingleListItem' component for each 'item' object. The 'SingleListItem' component receives props such as 'onClickHandler', 'text', 'index', and 'isSelected' from the 'List' component. The 'index' prop is used to identify each list item uniquely, while 'isSelected' is used to highlight the selected list item with a green background. By default, all list items have a red background color. To avoid unnecessary re-renders, the 'SingleListItem' component is memoized using the 'memo' function from React. This ensures that the component only re-renders when the value of the 'isSelected' prop changes, even if the user double-clicks on a list item. Overall, the 'List' component creates a list of items using the memoized 'SingleListItem' component, which efficiently renders each list item with the appropriate text and updates the selected item's background color.



2.	What problems / warnings are there with code?
	a. Uncaught TypeError: PropTypes.shapeOf is not a function, the propTypes declaration for the items prop in the WrappedListComponent component is wrong.
	Given Code:
	WrappedListComponent.propTypes = {
	  items: PropTypes.array(
	    PropTypes.shapeOf({
	      text: PropTypes.string.isRequired,
	    })
	  ),
	};
	Modified Code:
	WrappedListComponent.propTypes = {
	  items: PropTypes.arrayOf(
	    PropTypes.shape({
	      text: PropTypes.string.isRequired,
	    })
	  ),
	};
	
	b. The default value of items is set NULL which can lead to issues as we cannot use map function over that.
Given Code:
	WrappedListComponent.defaultProps = {
	  items: null,
};
	Modified Code:
	WrappedListComponent.defaultProps = {
	  items: [
	    {
	      text: 'First Item',
	    },
	    {
	      text: 'Second Item',
	    },
	    {
	      text: 'Third Item',
	    },
	  ],
	};
	
c. The onClickHandler property in WrappedSingleList Item is called incorrectly, the onClick handler is being called immediately when the component is rendered instead of when the component is clicked.
Given Code:
	const WrappedSingleListItem = ({ index, isSelected, onClickHandler, text }) => {
	  return (
	    <li
	      style={{ backgroundColor: isSelected ? 'green' : 'red' }}
	      onClick={onClickHandler(index)}
	    >
	      {text}
	    </li>
	  );
	};
	Modified Code:
	const WrappedSingleListItem = ({ index, isSelected, onClickHandler, text }) => {
	  return (
	    <li
	      style={{ backgroundColor: isSelected ? 'green' : 'red' }}
	      onClick={() => onClickHandler(index)}
    >
	      {text}
	    </li>
	  );
	};
d. Uncaught TypeError: setSelectedIndex is not a function in the code given, useState hook returns an array of two elements, with the first being the state value, and the second being the function that updates the state value. However, in the WrappedListComponent code, the setSelectedIndex function is being utilized as the state value itself, rather than the function to update the state value which is wrong.
	Given Code:
	const WrappedListComponent = ({ items }) => {
	  const [setSelectedIndex, selectedIndex] = useState();

	  useEffect(() => {
	    setSelectedIndex(null);
	  }, [items]);
	
	  const handleClick = (index) => {
	    setSelectedIndex(index);
	  };
	Modified Code:
	const WrappedListComponent = ({ items }) => {
	  const [selectedIndex, setSelectedIndex] = useState();
	  useEffect(() => {
	    setSelectedIndex(null);
	  }, [items]);
	
	  const handleClick = (index) => {
	setSelectedIndex(index);
	  };

e. The isSelected prop in the WrappedSingleListItem component is expected to be a boolean but is being passed the selectedIndex state value, which is a number.
	Given Code:
	<SingleListItem
	          key={index}
	          onClickHandler={() => handleClick(index)}
	          text={item.text}
	          index={index}
         isSelected={selectedIndex}
        />
	Modified Code:
	<SingleListItem
	          key={index}
	          onClickHandler={() => handleClick(index)}
          text={item.text}
	          index={index}
	          isSelected={index === selectedIndex}
	        />
f. Whenever we map the items there should be a unique key for each child but here it is not defined as a prop for each child, which results in a warning in console.
	Given Code:
	<SingleListItem
	          onClickHandler={() => handleClick(index)}
	          text={item.text}
	          index={index}
          isSelected={index === selectedIndex}
	        />
   Modified Code:
	<SingleListItem
          key={index}
          onClickHandler={() => handleClick(index)}
          text={item.text}
	          index={index}
          isSelected={index === selectedIndex}
	        />



3.Please fix, optimize, and/or modify the component as much as you think is necessary.
import React, { useState, useEffect, memo } from 'react';
import PropTypes from 'prop-types';

// Single List Item
const WrappedSingleListItem = ({ index, isSelected, onClickHandler, text }) => {
  return (
    <li
      style={{ backgroundColor: isSelected ? 'green' : 'red' }}
      onClick={() => onClickHandler(index)}
    >
      {text}
    </li>
  );
};

WrappedSingleListItem.propTypes = {
  index: PropTypes.number,
  isSelected: PropTypes.bool,
  onClickHandler: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired,
};

const SingleListItem = memo(WrappedSingleListItem);

// List Component
const WrappedListComponent = ({ items }) => {
  const [selectedIndex, setSelectedIndex] = useState();

  useEffect(() => {
    setSelectedIndex(null);
  }, [items]);

  const handleClick = (index) => {
    setSelectedIndex(index);
  };

  return (
    <ul style={{ textAlign: 'left' }}>
      {items.map((item, index) => (
        <SingleListItem
          key={index}
          onClickHandler={() => handleClick(index)}
          text={item.text}
          index={index}
          isSelected={index === selectedIndex}
        />
      ))}
    </ul>
  );
};

WrappedListComponent.propTypes = {
  items: PropTypes.arrayOf(
    PropTypes.shape({
      text: PropTypes.string.isRequired,
    })
  ),
};

WrappedListComponent.defaultProps = {
  items: [
    {
      text: 'First Item',
    },
    {
      text: 'Second Item',
    },
    {
      text: 'Third Item',
    },
  ],
};

const List = memo(WrappedListComponent);

export default List;


```
