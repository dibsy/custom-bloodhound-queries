### DCsync Custom Query

The generic inbuilt query for DCSync in Bloodhound Community is

```cypher
MATCH p=()-[:DCSync|AllExtendedRights|GenericAll]->(:Domain)
RETURN p
```
<img src="query1.png">

The above query do not return the other possible paths to carry out DCSync. For example it do not include edges with GetChanges and GetChangesAll permissions.
As obtained from BloodHound documentation, DS-Replication-Get-Changes-All permission in conjunction with DS-Replication-Get-Changes, a principal may perform a DCSync attack.

Hence we can modify the query a little bit to obtain those missing paths.

```cypher
MATCH p=()-[:DCSync|AllExtendedRights|GenericAll|GetChanges|GetChangesAll]->(:Domain)
RETURN p
```
<img src="query2.png">

Now we have more paths. However this returns few more additional nodes atleast one edge ( permission ) is present having either ```GetChanges``` and ```GetChangesAll```

We can narrow it down even further, to obtain the expected paths.

```cypher
MATCH (b)-[:GetChangesAll]->(:Domain)
WITH b MATCH (b)-[:GetChanges]->(:Domain)
MATCH (p)-[:DCSync|AllExtendedRights|GenericAll]->(:Domain)
MATCH y=(b)-[]->(:Domain)<-[]-(p)
RETURN y
```
<img src="finalquery.png">
