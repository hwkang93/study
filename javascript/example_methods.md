# 자주 사용하는 메서드 모음

### 선택된 체크박스 목록 가져오기

```html
<input type="checkbox" name="checkbox" value="A" checked>
<input type="checkbox" name="checkbox" value="B" checked>
<input type="checkbox" name="checkbox" value="C">
```

```javascript
function getCheckedValuesByName(name) {
	const resultList = [];
    const checkbox = document.getElementsByName(name);

	for(let i = 0; i <checkbox.length; i++) {
		if(checkbox[i].checked) {
			resultList.push(checkbox[i].value);
		}
	}

	return resultList;
}

const checkedValues = getCheckedValuesByName('checkbox');
console.log(checkedValues);
//[A, B]
```

