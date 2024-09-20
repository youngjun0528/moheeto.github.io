# 12. Form

## text

```HAML
<input v-model="message" placeholdere="여기를 수정해보세요.">
<p> Msg : {{message}}</p>
```

## checkbox

```HAML
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{checked}}</label>
```

## radio

```HAML
<input type="radio" id="radio_one" value="1" v-model="picked">
<label for="radio_one">One</label>
<input type="radio" id="radio_two" value="2" v-model="picked">
<label for="radio_two">Two</label>
<span>{{picked}}</span>
```

## select

```HAML
<select v-model="selected">
    <option disabled value="">Please Select One</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
</select>
<span>{{selected}}</span>
```