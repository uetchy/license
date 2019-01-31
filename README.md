# license

A simple zsh function generates `LICENSE` file from [license-templates](https://github.com/licenses/license-templates).

## Usage

```bash
license <license>
license apache
license cc0
license # default to `mit`
```

See [available licenses](https://github.com/licenses/license-templates/tree/master/templates).

## Source Code

```shell
license() {
  license_name=${@:-mit}
  endpoint="https://raw.githubusercontent.com/licenses/license-templates/master/templates/${license_name}.txt"

  year=$(date +%Y)
  username=$(git config user.name)
  email=$(git config user.email)
  organization="${username} <${email}>"

  license_body=$(curl -s ${endpoint})
  if [ $? != 0 ] || [ $license_body = "404: Not Found" ]; then
    echo "No such license: ${@}"
    return
  fi

  license_body=${license_body//"{{ year }}"/${year}}
  license_body=${license_body//"{{ organization }}"/${organization}}
  echo ${license_body} >LICENSE
  echo $license_body
  echo "\nSaved to ./LICENSE"
}
```
